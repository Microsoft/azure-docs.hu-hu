---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció Oracle PeopleSoft által védett, F5 BIG-IP APM használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és az Oracle PeopleSoft által védett F5 BIG-IP APM használatával.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 3af149f0c1db7f354be6bd968bbd0cf858493d4c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219297"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Oracle PeopleSoft-mel – védett az F5 BIG-IP APM használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az F5 BIG-IP APM és a Azure Active Directory (Azure AD) által védett Oracle PeopleSoft-védelmet. Ha az F5 BIG-IP APM és az Azure AD segítségével integrálja az Oracle PeopleSoft-védelmet, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér az F5 BIG-IP APM által védett Oracle PeopleSoft-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Oracle PeopleSoft-ba, az F5 BIG-IP APM és az Azure AD-fiókjuk által védettek.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.
* Ez az oktatóanyag az Oracle PeopleSoft ELM-re vonatkozó utasításokat tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

1. Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
1. Oracle PeopleSoft – az F5 BIG-IP APM egyszeri bejelentkezés (SSO) engedélyezett előfizetése által védett.

1. A közös megoldás üzembe helyezéséhez a következő licenc szükséges:

    1. F5 BIG-IP® legjobb csomag (vagy) 
    2. F5 BIG-IP Access Policy Manager™ (APM) önálló licenc 
    3. F5 BIG-IP Access Policy Manager™ (APM) bővítmény licence egy meglévő BIG-IP F5 BIG-IP® helyi Traffic Manager™ (LTM).
    4. A fenti licencen kívül az F5-es verziójú rendszerek is licenccel rendelkezhetnek a következővel: 
        * URL-szűrési előfizetés az URL-kategória adatbázisának használatára. 
        * F5 IP Intelligence-előfizetés az ismert támadók és a rosszindulatú forgalom észleléséhez és blokkolásához. 
        * Hálózati hardveres biztonsági modul (HSM) a digitális kulcsok erős hitelesítéshez való védelméhez és kezeléséhez.
1. Az F5 BIG-IP rendszer APM-modulokkal van kiépítve (a LTM nem kötelező).
1. Bár nem kötelező, erősen ajánlott az F5 rendszer üzembe helyezése egy [szinkronizálási/feladatátvételi eszköz csoportjában](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), amely magában foglalja az aktív készenléti párokat, valamint a magas rendelkezésre állású, LEBEGŐPONTOS IP-címet. A további illesztőfelület-redundancia a link aggregációs vezérlő protokoll (LACP) használatával érhető el. A LACP egyetlen virtuális csatolóként (összesítő csoportként) kezeli a csatlakoztatott fizikai adaptereket, és észleli a csoporton belüli összes illesztőfelületi hibát.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az F5 BIG-IP APM által védett Oracle PeopleSoft az **SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="add-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>Az F5 BIG-IP APM által védett Oracle PeopleSoft hozzáadása a katalógusból

Az F5 BIG-IP APM által az Azure AD-be védett Oracle PeopleSoft integrálásához hozzá kell adnia az F5 BIG-IP APM által védett Oracle PeopleSoft-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **Oracle PeopleSoft-Protected by F5 Big-IP APM** kifejezést a keresőmezőbe.
1. Válassza az **Oracle PeopleSoft által védett F5 Big-IP APM** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Az Azure AD SSO konfigurálása és tesztelése az F5 BIG-IP APM által védett Oracle PeopleSoft-hoz

Az Azure AD SSO konfigurálása és tesztelése az Oracle PeopleSoft használatával – az F5 BIG-IP APM által védett, **B. Simon** nevű felhasználói teszttel. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Oracle PeopleSoft által védett F5 BIG-IP APM használatával.

Az Azure AD SSO konfigurálásához és teszteléséhez az F5 BIG-IP APM által védett Oracle PeopleSoft használatával végezze el a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Oracle PeopleSoft-Protected konfigurálása az F5 Big-IP APM SSO használatával](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Oracle-PeopleSoft-Protected létrehozása az F5 Big-IP APM test User használatával](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** – hogy az Oracle PeopleSoft által védett B. Simon partnere legyen az F5 Big-IP APM, amely a felhasználó Azure ad-beli képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **Oracle PeopleSoft által védett, az F5 Big-IP APM** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<FQDN>.peoplesoft.f5.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. A **kijelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval, válasz URL-címmel és kijelentkezési URL-címmel. Vegye fel a kapcsolatot az [Oracle PeopleSoft által védett F5 Big-IP APM ügyfél-támogatási csapattal](https://support.f5.com) az érték beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az F5 BIG-IP APM-alkalmazás által védett Oracle PeopleSoft az SAML-jogkivonatokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútum-hozzárendeléseket adjon hozzá az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az F5 BIG-IP APM-alkalmazás által védett Oracle PeopleSoft szolgáltatás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name |  Forrás attribútum|
    | ------------------ | --------- |
    | EMPLID | User. Alkalmazottkód |

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban töltse le az **összevonási metaadatok XML-fájlját** és a **tanúsítványt (Base64)** , és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az F5 BIG-IP APM által védett Oracle PeopleSoft-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listájában válassza ki az **F5 Big-IP APM által védett Oracle PeopleSoft-védelemmel ellátott** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Az Oracle PeopleSoft-Protected konfigurálása F5 BIG-IP APM SSO használatával

### <a name="f5-saml-sp-configuration"></a>F5 SAML SP-konfiguráció

Importálja a metaadat-tanúsítványt az F5-re, amelyet később a telepítési folyamat során fog használni. Navigáljon a **System > tanúsítványkezelő > adatforgalom-tanúsítvány kezelése > SSL-tanúsítvány listára**. Válassza az **Importálás** lehetőséget a jobb oldali sarokban.

![F5 SAML SP-konfiguráció](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>Az SAML IDENTITÁSSZOLGÁLTATÓ-összekötő beállítása 

1. Navigáljon az **> összevonási > SAML: szolgáltató > külső identitásszolgáltató-összekötőhöz** , és kattintson **a > létrehozása metaadatokból** elemre.

    ![F5 SAML IDENTITÁSSZOLGÁLTATÓ-összekötő](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. Az alábbi lapon kattintson a **Tallózás** gombra az XML-fájl feltöltéséhez.

1. Adjon meg egy érvényes nevet az **identitás-szolgáltató neve** szövegmezőben, majd kattintson **az OK gombra**.

    ![Új SAML IDENTITÁSSZOLGÁLTATÓ-összekötő](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. Hajtsa végre a szükséges lépéseket a **biztonsági beállítások** lapon, majd kattintson az **OK gombra**.

    ![SAML IDENTITÁSSZOLGÁLTATÓ-összekötő szerkesztése](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>Az SAML SP beállítása

1. Navigáljon a **> összevonási > SAML-szolgáltató > helyi SP-szolgáltatások** lehetőségre, és kattintson a **Létrehozás** gombra. Hajtsa végre a következő információkat, majd kattintson **az OK** gombra.

    * Név: `<Name>`
    * Entitás azonosítója: `https://<FQDN>.peoplesoft.f5.com`
    * SP-név beállításai
        * Séma `https`
        * Gazdagép `<FQDN>.peoplesoft.f5.com`
        * Leírás `<Description>`

    ![Új SAML SP-szolgáltatások](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. Válassza ki az SP-konfigurációt, a PeopleSoftAppSSO, majd **kattintson a identitásszolgáltató-összekötők kötése/megszüntetése elemre**.
Kattintson az **új sor hozzáadása** elemre, és válassza ki az előző lépésben létrehozott **külső identitásszolgáltató-összekötőt** , kattintson a **frissítés** elemre, majd **az OK** gombra.

    ![SAML SP-szolgáltatások létrehozása](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>Alkalmazás konfigurálása

### <a name="create-a-new-pool"></a>Új készlet létrehozása
1. Navigáljon a **helyi forgalom > készletek > készlet listára**, kattintson a **Létrehozás** gombra, hajtsa végre a következő információkat, majd kattintson a **kész** gombra.

    * Név: `<Name>`
    * Leírás `<Description>`
    * Állapot-figyelők: `http`
    * Cím `<Address>`
    * Szolgáltatási port: `<Service Port>`

    ![új készlet létrehozása](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>Új ügyfél SSL-profil létrehozása

Navigáljon a **helyi forgalom > profilok > SSL > ügyfél > +**, hajtsa végre a következő információkat, majd kattintson a **kész** gombra.

* Név: `<Name>`
* Tanúsítvány `<Certificate>`
* Kulcs: `<Key>`

![új ügyfél SSL-profilja](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>Új virtuális kiszolgáló létrehozása

1. Navigáljon a **helyi forgalom > virtuális kiszolgálók > Virtual Server List > +**, hajtsa végre a következő információkat, majd kattintson a **kész** gombra.
    * Név: `<Name>`
    * Cél címe/maszk: `<Address>`
    * Szolgáltatási port: Port 443 HTTPS
    * HTTP-profil (ügyfél): http

    ![Új virtuális kiszolgáló létrehozása](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. Töltse ki a következő értékeket az alábbi oldalon:

    * SSL-profil (ügyfél): `<SSL Profile>`
    * Forrás címe fordítása: automatikus leképezés
    * Hozzáférési profil: `<Access Profile>`
    * Alapértelmezett készlet: `<Pool>`


    ![Új virtuális kiszolgáló PeopleSoft-készlet létrehozása](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>A PeopleSoft-alkalmazás beállítása az F5 Big-IP APM támogatására egyszeri bejelentkezési megoldásként

>[!Note]
> Referencia https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. Bejelentkezés a PeopleSoft-konzolra a `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` rendszergazdai hitelesítő adatokkal (például: PS/PS).

    ![Manager Önkiszolgálás](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. A PeopleSoft alkalmazásban hozzon létre **OAMPSFT** új felhasználói profilként, és rendeljen hozzá egy alacsony biztonsági szerepkört, például a **PeopleSoft-felhasználót**.
A **Peopletools > biztonsági > felhasználói profilok > felhasználói profilok** lehetőségre kattintva hozzon létre egy új felhasználói profilt, például: **OAMPSFT** , és adja hozzá a **PeopleSoft felhasználót**.

    ![PeopleSoft-felhasználó](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Nyissa meg a webes profilt, és adja meg a **OAMPSFT** nyilvános hozzáférésű **felhasználói azonosítóként**.

    ![Felhasználói profilok](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. A **PeopleTools alkalmazás-tervezőben** nyissa meg a **FUNCLIB_LDAP** rekordot.

    ![webprofil konfigurálása](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. Frissítse a felhasználói fejlécet a **PS_SSO_UID** for **OAMSSO_AUTHENTICATION** függvénnyel.
A **getWWWAuthConfig ()** függvényben cserélje le a **&defaultUserId** hozzárendelt értéket a webes profilban definiált **OAMPSFT** . Mentse a rekord definícióját.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. Nyissa meg a **bejelentkezik PeopleCode** lapot (PeopleTools, biztonság, biztonsági objektumok, bejelentkezés PeopleCode), és engedélyezze az **OAMSSO_AUTHENTICATION** függvényt – a bejelentkezés PeopleCode az Oracle Access Manager egyszeri bejelentkezéshez.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>Az F5 Big-IP APM beállítása a "PS_SSO_UID" HTTP-fejléc feltöltésére a PeopleSoft felhasználói azonosítójával

### <a name="configuring-per-request-policy"></a>Per-Request házirend konfigurálása
1. Navigáljon a **> profil/szabályzatok > Per-Request házirendek** elemre, kattintson a **Létrehozás** gombra, hajtsa végre a következő információkat, majd kattintson a **kész** gombra.

    * Név: `<Name>`
    * Profil típusa: ALL
    * Nyelvek `<Language>`

    ![Per-Request házirend konfigurálása ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. Kattintson **a Per-Request házirend szerkesztése** `<Name>` ![ Per-Request szabályzat PeopleSoftSSO elemre. ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>Per-Request házirend társítása a virtuális kiszolgálóhoz

Navigáljon a **helyi forgalom > virtuális kiszolgálók > Virtual Server List > PeopleSoftApp** `<Name>` Per-Request házirendként

![PeopleSoftSSO Per-Request szabályzatként ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>Az F5 Big-IP APM beállítása a PeopleSoft-alkalmazásból való egyszeri kijelentkezés támogatásához

Ha egyetlen kijelentkezési támogatást szeretne hozzáadni az összes PeopleSoft-felhasználóhoz, kövesse az alábbi lépéseket:

1. A PeopleSoft-portál helyes kijelentkezési URL-címének meghatározása
    * Annak megállapításához, hogy a PeopleSoft-alkalmazás hogyan használja a felhasználói munkamenetet, meg kell nyitnia a portált bármely webböngésző használatával, és engedélyeznie kell a böngésző hibakeresési eszközeit az alábbi példában látható módon:

        ![a PeopleSoft-portál kijelentkezési URL-címe ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * Keresse meg az azonosítót az `PT_LOGOUT_MENU` azonosítóval, és mentse az URL-útvonalat a lekérdezési paraméterekkel. A példánkban a következő érték van: `/psp/ps/?cmd=logout`

1. Hozzon létre olyan LTM-iRule, amely átirányítja a felhasználót az APM kijelentkezési URL-címére: `/my.logout.php3`

    * Navigáljon a **helyi forgalom > iRule**, kattintson a **Létrehozás** gombra, hajtsa végre a következő információkat, majd kattintson a **kész** gombra.

        Név: `<Name>`  
        Definíció:  
                    _Ha HTTP_REQUEST {switch-glob--[HTTP:: URI] { `/psp/ps/?cmd=logout` {http:: átirányítás `/my.logout.php3` }}}_

1. A létrehozott iRule társítása a virtuális kiszolgálóhoz

    * Navigáljon a **helyi forgalom > virtuális kiszolgálók > Virtual Server List > PeopleSoftApp > erőforrásaihoz**. Kattintson a **kezelés...** gombra   

    * Jelölje be `<Name>` az engedélyezett iRule lehetőséget, majd kattintson a **kész** gombra.

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * Adja meg a **név** szövegmező értékét a következőképpen `<Name>` 

        ![_iRule_PeopleSoftApp befejezve](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Oracle-PeopleSoft-Protected létrehozása az F5 BIG-IP APM test User használatával

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az Oracle PeopleSoft-Protected az F5 BIG-IP APM használatával. Az [F5 Big-IP APM-támogatással rendelkező Oracle-PeopleSoft-Protectedokkal](https://support.f5.com) az F5 Big-IP APM platform használatával adhatja hozzá a felhasználókat az Oracle PeopleSoft-Protectedhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja az Oracle PeopleSoft-Protected az F5 BIG-IP APM bejelentkezési URL-cím, amelyen kezdeményezheti a bejelentkezési folyamatot.  

* Nyissa meg az Oracle PeopleSoft-Protected az F5 BIG-IP APM bejelentkezési URL-címet, és indítsa el innen a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie az Oracle PeopleSoft-Protected az F5 Big-IP APM használatával, amelyhez be kell állítania az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazásokban az F5 BIG-IP APM csempével kattint az Oracle PeopleSoft-Protectedre, ha az SP módban van konfigurálva, a bejelentkezési folyamat elindításához és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie az Oracle-PeopleSoft-Protectedba az F5 BIG-IP APM használatával, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

Miután az F5 BIG-IP APM használatával konfigurálta az Oracle PeopleSoft-Protectedt, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
