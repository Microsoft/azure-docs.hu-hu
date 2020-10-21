---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Akamai | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Akamai között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: bbea6a0aa31034cd1c04145fb50b72432c9f8520
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319017"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Akamai

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Akamai a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Akamai-t az Azure AD-vel, a következőket teheti:

* A Akamai-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Akamai az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

A Azure Active Directory és a Akamai vállalati alkalmazás-hozzáférés integrációja lehetővé teszi a felhőben vagy a helyszínen üzemeltetett örökölt alkalmazásokhoz való zökkenőmentes hozzáférést. Az integrált megoldás a Azure Active Directory modern képességeinek előnyeit veszi igénybe, például az [Azure ad feltételes hozzáférését](../conditional-access/overview.md), [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md) és [Azure ad Identity Governance](../governance/identity-governance-overview.md) az alkalmazás módosításainak vagy ügynökének telepítése nélkül való hozzáféréshez.

Az alábbi ábrán az látható, hogy a Akamai EAA hogyan illeszkedik a szélesebb körű hibrid biztonságos hozzáférési forgatókönyvhöz

![A Akamai EAA illeszkedik a szélesebb körű hibrid biztonságos elérési helyzetbe](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>Legfontosabb hitelesítési forgatókönyvek

A modern hitelesítési protokollok (például az Open ID csatlakozás, az SAML és a WS-Fed) natív integrációs támogatása Azure Active Directory mellett a Akamai EAA az Azure AD-vel való belső és külső hozzáféréshez is kiterjeszti a biztonságos hozzáférést a régi alapú hitelesítési alkalmazásokhoz, így lehetővé teszi a modern forgatókönyvek (pl. jelszó nélküli hozzáférés) használatát az alkalmazások számára. Ide tartoznak az alábbiak:

* Fejléc alapú hitelesítési alkalmazások
* Távoli asztal
* SSH (Secure Shell)
* Kerberos-hitelesítési alkalmazások
* VNC (Virtual Network számítástechnika)
* Névtelen hitelesítés vagy nem beépített hitelesítési alkalmazások
* NTLM hitelesítési alkalmazások (védelem kettős kéréssel a felhasználó számára)
* Forms-Based alkalmazás (védelem kettős rákérdezéssel a felhasználó számára)

### <a name="integration-scenarios"></a>Integrációs forgatókönyvek

A Microsoft és a Akamai EAA együttműködése lehetővé teszi az üzleti igényeknek való megfelelést azáltal, hogy az üzleti követelmények alapján több integrációs forgatókönyvet támogat. Ezeket felhasználhatja az összes alkalmazás zéró napi lefedettségének biztosítására, valamint a megfelelő házirend-besorolások fokozatos osztályozására és konfigurálására.

#### <a name="integration-scenario-1"></a>1. integrációs forgatókönyv

Az Akamai EAA egyetlen alkalmazásként van konfigurálva az Azure AD-ben. A rendszergazda konfigurálhatja a HITELESÍTÉSSZOLGÁLTATÓI házirendet az alkalmazáson, és ha a feltételek teljesülnek, a felhasználók hozzáférhetnek a Akamai EAA-portálhoz.

**Profik**:

* Csak egyszer kell konfigurálnia a IDENTITÁSSZOLGÁLTATÓ

**Hátrányok**:

* A felhasználók két alkalmazás-portálon jelennek meg

* Egyetlen közös HITELESÍTÉSSZOLGÁLTATÓI házirend-lefedettség minden alkalmazáshoz.

![1. integrációs forgatókönyv](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>2. integrációs forgatókönyv

A Akamai EAA-alkalmazás beállítása külön történik az Azure AD-portálon. A rendszergazda beállíthatja, hogy az egyes HITELESÍTÉSSZOLGÁLTATÓI házirendeket az alkalmazás (ok) ra konfigurálja, és ha a feltételek teljesülnek, akkor a felhasználók közvetlenül átirányíthatók az adott alkalmazásba.

**Profik**:

* Egyéni HITELESÍTÉSSZOLGÁLTATÓI házirendeket is meghatározhat

* Az összes alkalmazás a 0365 Waffle és a myApps.microsoft.com panelen jelenik meg.


**Hátrányok**:

* Több IDENTITÁSSZOLGÁLTATÓ kell konfigurálnia.

![2. integrációs forgatókönyv](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Akamai egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

- A Akamai támogatja a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést

#### <a name="important"></a>Fontos

Az alábbiakban felsorolt összes beállítás megegyezik az **1** . és a 2. **forgatókönyvvel**. A **2. integrációs forgatókönyv** esetében egyéni identitásszolgáltató kell beállítania a Akamai EAA-ban, és az URL-tulajdonságot módosítani kell, hogy az alkalmazás URL-címére mutasson.

![Képernyőkép a AZURESSO-SP Általános lapján a Akamai vállalati alkalmazás-hozzáférésben. A hitelesítési konfiguráció URL-címe mező ki van emelve.](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>Akamai hozzáadása a gyűjteményből

A Akamai Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Akamai a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Akamai** kifejezést a keresőmezőbe.
1. Válassza ki a **Akamai** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Akamai

Konfigurálja és tesztelje az Azure AD SSO-t a Akamai a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Akamai-ben.

Az Azure AD SSO és a Akamai konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Akamai SSO konfigurálása](#configure-akamai-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[IDENTITÁSSZOLGÁLTATÓ beállítása](#setting-up-idp)**
    * **[Fejléc alapú hitelesítés](#header-based-authentication)**
    * **[Távoli asztal](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Kerberos-hitelesítés](#kerberos-authentication)**
    * **[Hozzon létre Akamai-teszt felhasználót](#create-akamai-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Akamai rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Akamai** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek lekéréséhez forduljon a Akamai ügyfélszolgálati [csapatához](https://www.akamai.com/us/en/contact-us/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Akamai beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Akamai.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Akamai**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-akamai-sso"></a>Akamai SSO konfigurálása

### <a name="setting-up-idp"></a>IDENTITÁSSZOLGÁLTATÓ beállítása

**AKAMAI EAA IDENTITÁSSZOLGÁLTATÓ konfigurációja**

1. Jelentkezzen be a **Akamai Enterprise Application Access** -konzolra.
1. Az **Akamai EAA konzolon**válassza az **identitás**  >  -**szolgáltatók** lehetőséget, majd kattintson az **identitás-szolgáltató hozzáadása**elemre.

    ![Képernyőkép a Akamai EAA konzol identitás-szolgáltatók ablakáról. Az identitás menüben válassza az Identitáskezelés lehetőséget, majd válassza az identitás-szolgáltató hozzáadása elemet.](./media/header-akamai-tutorial/configure01.png)

1. Az **új identitás-szolgáltató létrehozása** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőkép az új Identitáskezelés létrehozása párbeszédpanelről a Akamai EAA konzolon.](./media/header-akamai-tutorial/configure02.png)

    a. Adja meg az **egyedi nevet**.

    b. Válasszon **harmadik féltől származó SAML** -t, és kattintson az **Identity Provider létrehozása és konfigurálása**elemre.

### <a name="general-settings"></a>Általános beállítások

1. **Identitás feltartóztatása** – adja meg az Azure ad-konfigurációhoz használandó (SP Base URL-cím) nevét.

    > [!NOTE]
    > Dönthet úgy is, hogy saját egyéni tartományt használ (DNS-bejegyzést és tanúsítványt igényel). Ebben a példában a Akamai tartományt fogjuk használni.

1. **Akamai Cloud Zone** – válassza ki a megfelelő felhő zónát.
1. **Tanúsítvány érvényesítése** – a Akamai dokumentációjának ellenőrzése (nem kötelező)

    ![A Akamai EAA konzol általános lapjának képernyőképe, amely az identitások elfogására, a Akamai és a tanúsítvány érvényesítésére vonatkozó beállításokat jeleníti meg.](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Hitelesítési konfiguráció

1. URL-cím – megegyező URL-cím megadása az identitás feltartóztatva (ez az a hely, ahol a felhasználók a hitelesítés után átirányíthatók).
2. Kijelentkezési URL: frissítse a kijelentkezési URL-címet.
3. SAML-kérelem aláírása: az alapértelmezett beállítás nincs bejelölve.
4. A IDENTITÁSSZOLGÁLTATÓ metaadat-fájljához adja hozzá az alkalmazást az Azure AD-konzolon.

    ![Képernyőkép a Akamai EAA konzol hitelesítési konfigurációjáról, amely az URL-cím, a kijelentkezési URL-cím, az SAML-kérelem aláírása és a IDENTITÁSSZOLGÁLTATÓ metaadat-fájl beállításait mutatja.](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>Munkamenet-beállítások

Hagyja meg a beállításokat alapértelmezettként.

![Képernyőkép a Akamai EAA konzol munkamenet-beállításai párbeszédpanelről.](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>Könyvtárak

A címtár konfigurációjának kihagyása.

![Képernyőkép az Akamai EAA konzol könyvtárainak lapról.](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>Testreszabási felhasználói felület

A IDENTITÁSSZOLGÁLTATÓ testreszabását is felveheti.

![Képernyőkép a Akamai EAA konzol testreszabási lapjáról, amely a felhasználói felület, a nyelvi beállítások és a témák testreszabására vonatkozó beállításokat jeleníti meg.](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>Speciális beállítások

További részletekért tekintse meg az Advance Settings/Akamai dokumentációját.

![Képernyőkép a Akamai EAA konzol speciális beállítások lapjáról, amely az EAA-ügyfél, a speciális és a OIDC beállításait mutatja be az SAML-hidakhoz.](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>Üzembe helyezés

1. Kattintson az Identitáskezelés üzembe helyezése lehetőségre.

    ![A Akamai EAA konzol üzembe helyezési lapjának képernyőképe, amely a DENTING Provider üzembe helyezése gombra mutat.](./media/header-akamai-tutorial/deployment.png)

2. A telepítés sikerességének ellenőrzése

### <a name="header-based-authentication"></a>Fejléc alapú hitelesítés

Akamai-fejléc alapú hitelesítés

1. Válassza az **Egyéni http** -űrlap az alkalmazások hozzáadása varázslót.

    ![Képernyőkép a Akamai EAA konzol alkalmazások hozzáadása varázslójával, amely a hozzáférési alkalmazások szakaszban felsorolt CustomHTTP jeleníti meg.](./media/header-akamai-tutorial/configure05.png)

2. Adja meg az **alkalmazás nevét** és **leírását**.

    ![Képernyőkép az alkalmazás nevének és leírásának beállításait megjelenítő egyéni HTTP-alkalmazás párbeszédpanelről.](./media/header-akamai-tutorial/configure06.png)

    ![A Akamai EAA konzol általános lapjának képernyőképe, amely a MYHEADERAPP általános beállításait mutatja.](./media/header-akamai-tutorial/configure07.png)

    ![Képernyőkép a Akamai EAA-konzolról a tanúsítvány és a hely beállításainak megjelenítéséhez.](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Hitelesítés

1. Válassza a **hitelesítés** lapot.

    ![Képernyőkép a Akamai EAA konzolról, amelyen a hitelesítés lap van kiválasztva.](./media/header-akamai-tutorial/configure09.png)

2. Az **identitás-szolgáltató** kiosztása

    ![Képernyőkép a Akamai EAA konzol hitelesítés lapján a MYHEADERAPP, amely az identitás-szolgáltatót az Azure AD SSO-ra állítja.](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Szolgáltatások

Kattintson a Mentés gombra, és válassza a hitelesítés lehetőséget.

![Képernyőfelvétel a Akamai EAA konzol szolgáltatásai lapról, amely a jobb alsó sarokban található mentés és ugrás a AdvancedSettings MYHEADERAPP mutatja.](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Speciális beállítások

1. Az **ügyfél http-fejlécei**területen a **CustomerHeader** és az **SAML attribútumot**kell megadni.

    ![Képernyőkép a Akamai EAA konzoljának speciális beállítások lapjáról, amely az egyszeri bejelentkezésre vonatkozó naplózott URL-címet jeleníti meg a hitelesítés alatt.](./media/header-akamai-tutorial/configure12.png)

1. Kattintson **a Save (Mentés) gombra, és válassza a telepítés** gombot.

    ![Képernyőkép a Akamai EAA konzol speciális beállítások lapjáról, amely a jobb alsó sarokban található mentés és ugrás a központi telepítésre gombra mutat.](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

1. Kattintson az **alkalmazás központi telepítése** gombra.

    ![A Akamai EAA konzol üzembe helyezés lapjának képernyőképe, amely az alkalmazás központi telepítése gombra mutat.](./media/header-akamai-tutorial/configure14.png)

1. Ellenőrizze, hogy az alkalmazás telepítése sikeresen megtörtént-e.

    ![Képernyőkép a Akamai EAA konzol üzembe helyezés lapján az alkalmazás állapota üzenet: "alkalmazás sikeresen telepítve".](./media/header-akamai-tutorial/configure15.png)

1. End-User felhasználói élmény.

    ![Képernyőkép a myapps.microsoft.com megnyitási képernyőjéről, háttérbeli képpel és bejelentkezési párbeszédpanellel.](./media/header-akamai-tutorial/enduser01.png)

    ![Képernyőfelvétel: a beépülő modul, a HRWEB, a Akamai-CorpApps, a költségek, a csoportok és a hozzáférési felülvizsgálatok ikonjait megjelenítő alkalmazások ablakának része. ](./media/header-akamai-tutorial/enduser02.png)

1. Feltételes hozzáférés.

    ![Képernyőkép az üzenetről: bejelentkezési kérelem jóváhagyása. Küldtünk egy értesítést a mobileszközön. A folytatáshoz válaszoljon.](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![Képernyőkép az alkalmazások képernyőjéről, amely egy ikont jelenít meg a MyHeaderApp.](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>Távoli asztal

1. Az alkalmazások hozzáadása varázslóban válassza az **RDP** elemet.

    ![Képernyőkép a Akamai EAA konzol alkalmazások hozzáadása varázslójával, amely a hozzáférési alkalmazások szakaszban szereplő alkalmazások közötti RDP-t mutatja.](./media/header-akamai-tutorial/configure16.png)

1. Adja meg az **alkalmazás nevét** és **leírását**.

    ![Képernyőkép: az alkalmazás nevének és leírásának beállításait megjelenítő RDP-alkalmazás párbeszédpanel.](./media/header-akamai-tutorial/configure17.png)

    ![A Akamai EAA konzoljának általános lapjának képernyőképe, amely a SECRETRDPAPP alkalmazás-identitási beállításait mutatja.](./media/header-akamai-tutorial/configure18.png)

1. Itt adhatja meg a karbantartáshoz használandó összekötőt.

    ![Képernyőkép a Akamai EAA-konzolról a tanúsítvány és a hely beállításainak megjelenítéséhez. A társított összekötők beállítása USWST-CON1.](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Hitelesítés

Kattintson **a Mentés gombra, és válassza a szolgáltatások**lehetőséget.

![Képernyőfelvétel: a Akamai EAA konzol hitelesítés lapja, amelyen a SECRETRDPAPP a Save and go to Services (Mentés és ugrás szolgáltatások) gomb látható a jobb alsó sarokban.](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Szolgáltatások

Kattintson **a Save (Mentés) gombra, és válassza a speciális beállítások lehetőséget**.

![Képernyőfelvétel a Akamai EAA konzol szolgáltatásai lapról, amely a jobb alsó sarokban található mentés és ugrás a AdvancedSettings SECRETRDPAPP mutatja.](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Speciális beállítások

1. Kattintson **a Mentés gombra, és válassza a telepítés**lehetőséget.

    ![Képernyőkép a Akamai EAA konzoljának speciális beállítások lapján a távoli asztal konfigurációjának beállításait megjelenítő SECRETRDPAPP.](./media/header-akamai-tutorial/configure22.png)

    ![Képernyőkép a Akamai EAA konzoljának speciális beállítások lapján a SECRETRDPAPP, amely a hitelesítés és az állapot-ellenőrzési konfiguráció beállításait mutatja.](./media/header-akamai-tutorial/configure23.png)

    ![Képernyőkép a Akamai EAA konzol egyéni HTTP-fejlécek beállításairól a SECRETRDPAPP és a jobb alsó sarokban található mentés és ugrás a telepítéshez gombra kattintva.](./media/header-akamai-tutorial/configure24.png)

1. End-Useri élmény

    ![Képernyőkép egy myapps.microsoft.com ablakról, háttérbeli képpel és bejelentkezési párbeszédpanellel.](./media/header-akamai-tutorial/enduser03.png)

    ![Képernyőkép a myapps.microsoft.com apps ablakról, amely a beépülő modul, a HRWEB, a Akamai-CorpApps, a költségek, a csoportok és a hozzáférési felülvizsgálatok ikonjaival rendelkezik.](./media/header-akamai-tutorial/enduser02.png)

1. Feltételes hozzáférés

    ![Képernyőkép a feltételes hozzáférési üzenetről: a bejelentkezési kérelem jóváhagyása. Küldtünk egy értesítést a mobileszközön. A folytatáshoz válaszoljon.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Képernyőkép az alkalmazások képernyőjéről, amely a MyHeaderApp és a SecretRDPApp ikonjait jeleníti meg.](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Képernyőkép a Windows Server 2012 RS képernyőről, amely általános felhasználói ikonokat jelenít meg. A rendszergazda, a user0 és a Felhasználó1 ikonjai azt mutatják be, hogy be vannak jelentkezve.](./media/header-akamai-tutorial/conditionalaccess06.png)

1. Másik lehetőségként közvetlenül is beírhatja az RDP-alkalmazás URL-címét.

#### <a name="ssh"></a>SSH

1. Lépjen az alkalmazások hozzáadása elemre, és válassza az **SSH**lehetőséget.

    ![Képernyőkép a Akamai EAA konzol alkalmazások hozzáadása varázslójával, amely a hozzáférési alkalmazások szakasz alkalmazások között felsorolt SSH-t jeleníti meg.](./media/header-akamai-tutorial/configure25.png)

1. Adja meg az **alkalmazás nevét** és **leírását**.

    ![Képernyőkép – az alkalmazás nevének és leírásának beállításait megjelenítő SSH-alkalmazás párbeszédpanel.](./media/header-akamai-tutorial/configure26.png)

1. Adja meg az alkalmazás identitását.

    ![A Akamai EAA konzol általános lapjának képernyőképe, amely az alkalmazás-identitás beállításait mutatja az SSH-SECURE számára.](./media/header-akamai-tutorial/configure27.png)

    a. Adja meg a nevet és a leírást.

    b. Az alkalmazáskiszolgáló IP/FQDN és portjának megadása az SSH-hoz.

    c. A Akamai EAA megadása az SSH-Felhasználónév/jelszó beállításnál.

    d. Adja meg a külső állomásnév nevét.

    e. Adja meg az összekötő helyét, és válassza ki az összekötőt.

#### <a name="authentication"></a>Hitelesítés

Kattintson a **Save (Mentés**) gombra, és válassza a szolgáltatások lehetőséget.

![Képernyőkép a Akamai EAA konzol hitelesítés lapjáról, amely a jobb alsó sarokban található mentés és ugrás szolgáltatások gombra mutat.](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Szolgáltatások

Kattintson **a Save (Mentés) gombra, és válassza a speciális beállítások lehetőséget**.

![Képernyőkép az Akamai EAA Console Services lapról – biztonságos, a jobb alsó sarokban található mentés és ugrás a AdvancedSettings gombra kattintva.](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Speciális beállítások

Kattintson a Mentés gombra, és lépjen a központi telepítésre

![Képernyőkép a Akamai EAA konzoljának speciális beállítások lapjáról, amely a hitelesítés és az állapot-ellenőrzési konfiguráció beállításait mutatja.](./media/header-akamai-tutorial/configure30.png)

![Képernyőfelvétel: az Akamai EAA konzoljának egyéni HTTP-fejléc-beállításai az SSH-SECURE számára a jobb alsó sarokban található mentés és ugrás a központi telepítésre gombra.](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Üzembe helyezés

1. Kattintson az **alkalmazás központi telepítése**elemre.

    ![Képernyőkép a Akamai EAA konzol üzembe helyezési lapjáról az SSH-SECURE szolgáltatáshoz, amely az alkalmazás központi telepítése gombra mutat.](./media/header-akamai-tutorial/configure32.png)

1. End-Useri élmény

    ![Képernyőkép a myapps.microsoft.com ablakos bejelentkezési párbeszédpanelről.](./media/header-akamai-tutorial/enduser03.png)

    ![Képernyőkép az alkalmazások ablakról a beépülő modul, a HRWEB, a Akamai-CorpApps, a költségek, a csoportok és a hozzáférési felülvizsgálatok ikonjait bemutató myapps.microsoft.com.](./media/header-akamai-tutorial/enduser04.png)

1. Feltételes hozzáférés

    ![Az üzenetet bemutató képernyőkép: bejelentkezési kérelem jóváhagyása. Küldtünk egy értesítést a mobileszközön. A folytatáshoz válaszoljon.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Képernyőkép az alkalmazások képernyőjéről, amely a MyHeaderApp, az SSH Secure és a SecretRDPApp ikonjait jeleníti meg.](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![Képernyőkép a ssh-secure-go.akamai-access.com, amely a jelszó megadását mutatja be.](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![Képernyőkép a ssh-secure-go.akamai-access.com, amely az alkalmazással kapcsolatos információkat és a parancsok megadását jeleníti meg.](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Kerberos-hitelesítés

Az alábbi példában egy belső webkiszolgálót teszünk közzé <code>http://frp-app1.superdemo.live</code> , és engedélyezi az SSO-t a KCD használatával

#### <a name="general-tab"></a>Általános lap

![A MYKERBOROSAPP Akamai EAA konzoljának általános lapjának képernyőképe.](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>Hitelesítés lap

Az identitás-szolgáltató kiosztása

![Képernyőkép az Akamai EAA konzol hitelesítés lapján a MYKERBOROSAPP, amely az identitás-szolgáltatót az Azure AD SSO értékre állítja be.](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>Szolgáltatások lap

![Képernyőfelvétel a MYKERBOROSAPP Akamai EAA konzol szolgáltatásainak lapjáról.](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>Speciális beállítások

![Képernyőkép a Akamai EAA konzoljának speciális beállítások lapján a MYKERBOROSAPP kapcsolódó alkalmazások és hitelesítés beállításainak megjelenítéséhez.](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> A webkiszolgáló SPN SPN@Domain formátuma a következő: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` ebben a bemutatóban. Hagyja meg az alapértelmezett beállításokat a többi beállításnál.

#### <a name="deployment-tab"></a>Üzembe helyezés lap

![Képernyőfelvétel: az Akamai EAA konzoljának üzembe helyezési lapja az alkalmazás központi telepítése gombra mutató MYKERBOROSAPP.](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>Könyvtár hozzáadása

1. Válassza az **ad** lehetőséget a legördülő listából.

    ![Képernyőkép a Akamai EAA konzol könyvtárak ablakáról, amely egy új címtár létrehozása párbeszédpanelt jelenít meg a legördülő listából a könyvtár típusa mezőben.](./media/header-akamai-tutorial/configure33.png)

1. Adja meg a szükséges adatértékeket.

    ![Képernyőkép a Akamai EAA konzol SUPERDEMOLIVE ablakáról, amely a könyvtárnév, a címtárszolgáltatás, az összekötő és az attribútumok hozzárendelésének beállításait tartalmazza.](./media/header-akamai-tutorial/configure34.png)

1. Ellenőrizze a címtár létrehozását.

    ![Képernyőkép a Akamai EAA konzol könyvtárainak ablakáról, amely azt mutatja, hogy a könyvtár-főbemutató. Live hozzá lett adva.](./media/header-akamai-tutorial/directorydomain.png)

1. Adja hozzá azokat a csoportokat/szervezeti egységeket, akiknek hozzáférésre van szükségük.

    ![Képernyőkép a könyvtár főbemutatójának beállításairól. élő. A csoportok vagy szervezeti egységek hozzáadására kiválasztott ikon ki van emelve.](./media/header-akamai-tutorial/addgroup.png)

1. Az alábbi csoport neve EAAGroup, és 1 taggal rendelkezik.

    ![Képernyőkép a Akamai EAA-konzol CSOPORTJAIról a SUPERDEMOLIVE könyvtár ablakban. A EAAGroup 1 felhasználóval szerepelnek a csoportok területen.](./media/header-akamai-tutorial/eaagroup.png)

1. Adja hozzá a könyvtárat a személyazonosság-szolgáltatóhoz az **identitás**  >  -**szolgáltatók** elemre kattintva, majd kattintson a **címtárak** fülre, és kattintson a **hozzárendelés könyvtár**elemre.

    ![Képernyőkép az Azure AD SSO-hez készült Akamai EAA konzol könyvtárainak lapról, amely a főbemutatót jeleníti meg. az aktuálisan hozzárendelt könyvtárak listájának megjelenítése.](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>KCD delegálás konfigurálása az EAA-hoz – útmutató

#### <a name="step-1-create-an-account"></a>1. lépés: fiók létrehozása 

1. A példában a **EAADelegation**nevű fiókot fogjuk használni. Ezt a **Active Directory felhasználók és számítógépek** snappin hajthatja végre.

    ![Képernyőkép az Azure AD SSO-hoz készült Akamai EAA konzol könyvtárainak lapról. A könyvtár-főbemutató. Live a jelenleg hozzárendelt könyvtárak területen található.](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > A felhasználónévnek megadott formátumúnak kell lennie az **identitást elfogó név**alapján. Az 1. ábrán láthatjuk, hogy **corpapps.login.go.Akamai-Access.com**

1. A felhasználói bejelentkezési név a következőket teszi:`HTTP/corpapps.login.go.akamai-access.com`

    ![A "EAADelegation" értékre beállított EAADelegation tulajdonságok és a felhasználói bejelentkezési név beállítása a következőre: HTTP/corpapps. login. go. Akamai-Access. com.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>2. lépés: az egyszerű szolgáltatásnév konfigurálása ehhez a fiókhoz

1. A minta alapján az egyszerű szolgáltatásnév az alábbi lesz.

2. Setspn-s **http/corpapps. login. go. Akamai-Access. com eaadelegation**

    ![Képernyőkép egy rendszergazdai parancssorból, amely a Setspn-s http/corpapps. login. go. Akamai-Access. com eaadelegation parancs eredményeit jeleníti meg.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>3. lépés: a delegálás konfigurálása

1. A EAADelegation-fiókhoz kattintson a delegálás fülre.

    ![Az egyszerű szolgáltatásnév konfigurálására szolgáló parancsot bemutató rendszergazdai parancssor képernyőképe.](./media/header-akamai-tutorial/spn.png)

    * A bármely hitelesítési protokoll használata
    * Kattintson a Hozzáadás gombra, és adja hozzá a Kerberos-webhely alkalmazáskészlet-fiókját. Ha megfelelően van konfigurálva, automatikusan a megfelelő SPN-re kell feloldania.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>4. lépés: keytab kiterjesztésű-fájl létrehozása a AKAMAI EAA-hoz

1. Az általános szintaxist itt találja.

1. ktpass/out ActiveDirectorydomain. keytab kiterjesztésű/Princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass + RdnPass/crypto minden/ptype KRB5_NT_PRINCIPAL

1. Példa

    | Snippet | Magyarázat |
    | - | - |
    | Ktpass/out EAADemo. keytab kiterjesztésű | A kimeneti keytab kiterjesztésű fájljának neve |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | EAA-delegálási fiók |
    | /pass RANDOMPASS | EAA-delegálási fiók jelszava |
    | /crypto minden ptype KRB5_NT_PRINCIPAL | a Akamai EAA dokumentációjának összeegyeztetése |
    | | |

1. Ktpass/out EAADemo. keytab kiterjesztésű/Princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS/crypto minden ptype KRB5_NT_PRINCIPAL

    ![Képernyőkép a rendszergazdai parancssorról, amely a AKAMAI EAA keytab kiterjesztésű-fájljának létrehozására szolgáló parancs eredményét jeleníti meg.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>5. lépés: a keytab kiterjesztésű importálása a AKAMAI EAA konzolon

1. Kattintson **a**  >  **Rendszerlapok**elemre.

    ![Képernyőkép az Akamai EAA-konzolról, amely a rendszer menüjéből kiválasztott főlapokat jeleníti meg.](./media/header-akamai-tutorial/keytabs.png)

1. A keytab kiterjesztésű típusnál válassza a **Kerberos-delegálás**lehetőséget.

    ![Képernyőkép a Akamai EAA konzol EAAKEYTAB képernyőjéről, amely a keytab kiterjesztésű beállításait mutatja. A keytab kiterjesztésű típusa Kerberos-delegálásra van beállítva.](./media/header-akamai-tutorial/keytabdelegation.png)

1. Győződjön meg arról, hogy a keytab kiterjesztésű üzembe helyezettként és ellenőrzöttként jelenik meg.

    ![Képernyőkép a Akamai EAA konzoljának LAPFÜLeit tartalmazó képernyőről, amely az EAA-keytab kiterjesztésű "keytab kiterjesztésű üzembe helyezett és ellenőrzött" néven jelenik meg.](./media/header-akamai-tutorial/keytabs02.png)

1. Felhasználó felület

    ![Képernyőkép a bejelentkezési párbeszédpanelről a myapps.microsoft.com címen. ](./media/header-akamai-tutorial/enduser03.png)

    ![Képernyőkép az alkalmazások myapps.microsoft.com az alkalmazás ikonjait megjelenítő ablakban.](./media/header-akamai-tutorial/enduser04.png)

1. Feltételes hozzáférés

    ![A jóváhagyást kérő bejelentkezési üzenetet bemutató képernyőkép. az üzenet.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Képernyőkép az alkalmazások képernyőjéről, amely a MyHeaderApp, az SSH Secure, a SecretRDPApp és a myKerberosApp ikonjait jeleníti meg.](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![Képernyőfelvétel a myKerberosApp tartozó Splash-képernyőről. A "Welcome superdemo\user1" üzenet egy háttérképen jelenik meg.](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>Akamai-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Akamai-ben. A [Akamai ügyfél-támogatási csapattal](https://www.akamai.com/us/en/contact-us/) együttműködve veheti fel a felhasználókat a Akamai-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Akamai csempére kattint, automatikusan be kell jelentkeznie arra a Akamai, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Akamai kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)