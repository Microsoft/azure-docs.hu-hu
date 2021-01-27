---
title: 'Oktatóanyag: az Azure AD egyszeri bejelentkezéses integrációja F5-mel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést (SSO) Azure Active Directory és F5 között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: 139366277feab0a04c47311b4f0210223b2bc8d9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98892014"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az F5-mel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az F5-et Azure Active Directory (Azure AD) használatával. Az F5 és az Azure AD integrálásával a következőket teheti:

* Az F5-hez hozzáférő Azure AD-beli vezérlés.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek az F5-be az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

* F5 egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

* A közös megoldás üzembe helyezéséhez a következő licenc szükséges:
    * F5 BIG-IP – &reg; legjobb csomag (vagy)

    * F5 BIG-IP Access Policy Manager &trade; (APM) önálló licenc

    * F5 BIG-IP Access Policy Manager &trade; (APM) bővítmény licence meglévő Big-IP F5 Big-IP &reg; helyi Traffic Manager &trade; (LTM).

    * A fenti licencen kívül az F5-es verziójú rendszerek is licenccel rendelkezhetnek a következővel:

        * URL-szűrési előfizetés az URL-kategória adatbázisának használatára

        * F5 IP Intelligence-előfizetés az ismert támadók és a rosszindulatú forgalom észleléséhez és blokkolásához

        * Hálózati hardveres biztonsági modul (HSM) a digitális kulcsok erős hitelesítéshez való védelméhez és kezeléséhez

* Az F5 BIG-IP rendszer APM-modulokkal van kiépítve (a LTM nem kötelező)

* Bár nem kötelező, erősen ajánlott az F5 rendszer üzembe helyezése egy [szinkronizálási/feladatátvételi eszköz csoportjában](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), amely magában foglalja az aktív készenléti párokat, valamint a magas rendelkezésre állású, LEBEGŐPONTOS IP-címet. A további illesztőfelület-redundancia a link aggregációs vezérlő protokoll (LACP) használatával érhető el. A LACP egyetlen virtuális csatolóként (összesítő csoportként) kezeli a csatlakoztatott fizikai adaptereket, és észleli a csoporton belüli összes illesztőfelületi hibát.

* Kerberos-alkalmazások esetén egy helyszíni AD-szolgáltatásfiók a korlátozott delegáláshoz.  Az AD delegálási fiók létrehozásához tekintse meg az [F5 dokumentációt](https://support.f5.com/csp/article/K43063049) .

## <a name="access-guided-configuration"></a>Hozzáférés az irányított konfigurációhoz

* A hozzáférés-vezérelt konfiguráció az F5 TMOS 13.1.0.8 vagy újabb verzióban támogatott. Ha a BIG-IP rendszer a 13.1.0.8 alábbi verzióját futtatja, tekintse meg a **Speciális konfiguráció** szakaszt.

* A hozzáférés-vezérelt konfiguráció egy teljesen új és áramvonalas felhasználói élményt nyújt. Ez a munkafolyamat-alapú architektúra a kiválasztott topológiához igazított, intuitív és újra belépő konfigurációs lépéseket biztosít.

* Mielőtt folytatná a konfigurálást, frissítse az irányított konfigurációt úgy, hogy letölti a legújabb használati eset csomagot a [downloads.F5.com](https://login.f5.com/resource/login.jsp?ctx=719748)webhelyről. A frissítéshez kövesse az alábbi eljárást.

    >[!NOTE]
    >Az alábbi Képernyőképek a legújabb verzióra vonatkoznak (BIG-IP 15,0, AGC-verzió: 5,0). Az alábbi konfigurációs lépések érvényesek erre a használati esetre a 13.1.0.8 és a legújabb BIG-IP-verzió között.

1. Az F5 BIG-IP webes felületen kattintson a **hozzáférés >> útmutató konfiguráció** elemre.

2. Az **irányított konfiguráció** lapon kattintson az **irányított konfiguráció frissítése** elemre a bal felső sarokban.

    ![Képernyőkép, amely az "irányított konfiguráció" oldalt mutatja be a "vezérelt irányított konfiguráció" művelet kiválasztásával.](./media/kerbf5-tutorial/configure14.png) 

3. A frissítési útmutató konfigurációjának pop képernyőjén válassza a **fájl kiválasztása** elemet a letöltött használati eset csomag feltöltéséhez, majd kattintson a **feltöltés és telepítés** gombra.

    ![Képernyőfelvétel: "a fájl kiválasztása" és a "feltöltés és telepítés" beállítású előugró ablak "](./media/kerbf5-tutorial/configure15.png) 

4. A frissítés befejezésekor kattintson a **Continue (folytatás** ) gombra.

    ![Képernyőfelvétel: az "irányított konfiguráció frissítése befejeződött" párbeszédpanel és a "Folytatás" gomb van kiválasztva.](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* F5 **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés támogatása
* Az F5 SSO három különböző módon konfigurálható.

- [Az F5 egyszeri bejelentkezés konfigurálása a Kerberos-alkalmazáshoz](#configure-f5-single-sign-on-for-kerberos-application)

- [Az F5 egyszeri bejelentkezés konfigurálása a fejléc-alapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezés konfigurálása a speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Legfontosabb hitelesítési forgatókönyvek

A modern hitelesítési protokollok (például az Open ID csatlakozás, az SAML és a WS-Fed) natív integrációs támogatása Azure Active Directory mellett az F5 az Azure AD-vel való belső és külső hozzáféréshez is biztosítja az örökölt hitelesítési alkalmazások biztonságos hozzáférését, így a modern forgatókönyvek (például jelszó nélküli hozzáférés) lehetővé teszik ezeket az alkalmazásokat. Ilyenek például a következők:

* Fejléc alapú hitelesítési alkalmazások

* Kerberos-hitelesítési alkalmazások

* Névtelen hitelesítés vagy nem beépített hitelesítési alkalmazások

* NTLM hitelesítési alkalmazások (védelem kettős kéréssel a felhasználó számára)

* Űrlap-alapú alkalmazás (védelem kettős rákérdezéssel a felhasználó számára)

## <a name="adding-f5-from-the-gallery"></a>Az F5 hozzáadása a gyűjteményből

Az F5 Azure AD-integrációjának konfigurálásához az F5 billentyűt kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **F5** kifejezést.
1. Válassza az **F5** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése F5 rendszerhez

Konfigurálja és tesztelje az Azure AD SSO-t az F5 használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az F5 billentyűvel.

Az Azure AD SSO az F5 használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[F5 SSO konfigurálása](#configure-f5-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre F5 test User](#create-f5-test-user)** – ha a felhasználó Azure ad-képviseletéhez társított B
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **F5** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YourCustomFQDN>.f5.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YourCustomFQDN>.f5.com/`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A következő értékek megszerzéséhez lépjen kapcsolatba az [F5 ügyfél-támogatási csapattal](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML** és **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **F5 beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az F5 elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **F5** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.
1. Kattintson a **feltételes hozzáférés** lehetőségre.
1. Kattintson az **új házirend** elemre.
1. Mostantól megtekintheti az F5 alkalmazás erőforrásként a feltételes hozzáférési házirendet, és alkalmazhatja a feltételes hozzáférést, beleértve a többtényezős hitelesítést, az eszközön alapuló hozzáférés-vezérlést vagy az Identity Protection-házirendet.

## <a name="configure-f5-sso"></a>Az F5 SSO konfigurálása

- [Az F5 egyszeri bejelentkezés konfigurálása a fejléc-alapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezés konfigurálása a speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Az F5 egyszeri bejelentkezés konfigurálása a Kerberos-alkalmazáshoz

### <a name="guided-configuration"></a>Irányított konfiguráció

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az F5 (Kerberos) vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Importálnia kell a metaadat-tanúsítványt az F5 rendszerbe, amelyet később a telepítési folyamat során fog használni.

1. Navigáljon a **System > tanúsítványkezelő > adatforgalom-tanúsítvány kezelése > SSL-tanúsítvány listára**. Válassza az **Importálás** lehetőséget a jobb oldali sarokban. Adja meg a **tanúsítvány nevét** (a konfiguráció későbbi részében lesz hivatkozva). A **tanúsítvány forrása** területen válassza a fájl feltöltése lehetőséget, hogy az SAML egyszeri bejelentkezés konfigurálásakor az Azure-ból letöltött tanúsítványt adja meg. Kattintson az **Importálás** gombra.

    ![Képernyőkép, amely megjeleníti az "S S L tanúsítvány/kulcs forrása" lapot a "tanúsítvány neve" kiemelve, "fájl feltöltése" és az "Importálás" gomb kiválasztásával.](./media/kerbf5-tutorial/configure01.png) 

1. Emellett **SSL-tanúsítványra lesz szüksége az alkalmazás állomásneve számára. Navigáljon a System > tanúsítványkezelő > adatforgalom-tanúsítvány kezelése > SSL-tanúsítvány listára**. Válassza az **Importálás** lehetőséget a jobb oldali sarokban. AZ **Importálás típusa** **PKCS 12 (IIS)** lesz. Adja meg a **kulcs nevét** (a konfiguráció későbbi részében lesz hivatkozva), majd adja meg a pfx-fájlt. A PFX-fájl **jelszavának** megadása. Kattintson az **Importálás** gombra.

    >[!NOTE]
    >A példában az alkalmazás neve az `Kerbapp.superdemo.live` , hogy egy wild card-tanúsítványt használunk a kulcsnév `WildCard-SuperDemo.live`

    ![Képernyőkép, amely megjeleníti az "S S L tanúsítvány/kulcs forrása" lapot a beírt értékekkel és az "Importálás" gomb kiválasztásával.](./media/kerbf5-tutorial/configure02.png) 
 
1. Az Azure AD-összevonás és az alkalmazás-hozzáférés beállításához az interaktív élményt fogjuk használni. Ugrás az – F5 BIG-IP **Main** elemre, és válassza a **hozzáférés > irányított konfiguráció > összevonási > SAML-szolgáltató** elemet. Kattintson a **tovább** gombra, majd a konfigurálás megkezdéséhez kattintson a **tovább** gombra.

    ![Képernyőkép, amely az "összevont konfiguráció" ikont jeleníti meg a Kiemelt "összevonás" ikonnal és az "S L Service Provider" kiválasztásával.](./media/kerbf5-tutorial/configure03.png) 

    ![Képernyőkép, amely a "Next" gomb kiválasztásával jeleníti meg az "irányított konfiguráció-S A M L Service Provider" lapot.](./media/kerbf5-tutorial/configure04.png)

1. Adja meg a **konfiguráció nevét**. Határozza meg az **entitás azonosítóját** (ugyanaz, mint amit az Azure ad-alkalmazás konfigurációjában konfigurált). Adja meg az **állomásnév nevét**. Adja meg a hivatkozás **leírását** . Fogadja el a fennmaradó alapértelmezett bejegyzéseket, és válassza ki, majd kattintson a **mentés & tovább** gombra.

    ![Képernyőfelvétel: "állomásnév" és "Description" szövegmezők, valamint a "Mentés & következő" gomb kiválasztásakor megjelenik a "szolgáltató tulajdonságai".](./media/kerbf5-tutorial/configure05.png) 

1. Ebben a példában egy új virtuális kiszolgálót hozunk létre a 443-as port 192.168.30.200. A **célként megadott címben** válassza a virtuális kiszolgáló IP-címét. Válassza ki az ügyfél **SSL-profilját**, majd válassza az új létrehozása lehetőséget. Adja meg a korábban feltöltött alkalmazás tanúsítványát (a példában szereplő Wild Card-tanúsítványt) és a társított kulcsot, majd kattintson a **mentés & tovább** gombra.

    >[!NOTE]
    >Ebben a példában a belső webkiszolgáló a 80-as porton fut, és a 443-as verzióban szeretné közzétenni.

    ![Képernyőkép, amely a "virtuális kiszolgáló tulajdonságai" lapot jeleníti meg a "cél címe" szövegmezővel, és a "Mentés & tovább" gomb van kiválasztva.](./media/kerbf5-tutorial/configure06.png)

1. A **identitásszolgáltató-összekötő konfigurálásához válassza a módszer lehetőséget**, majd adja meg a metaadatokat, kattintson a fájl kiválasztása lehetőségre, és töltse fel az Azure ad-ből korábban letöltött metaadat-XML-fájlt. Adja meg az SAML IDENTITÁSSZOLGÁLTATÓ-összekötő egyedi **nevét** . Válassza ki a korábban feltöltött **metaadat-aláíró tanúsítványt** . Kattintson a **mentés & tovább** gombra.

    ![Képernyőkép: a "külső identitás-szolgáltatói összekötő beállításai" oldal, a "név" szövegmező kiemelve és a "Mentés & tovább" gomb kiválasztásával.](./media/kerbf5-tutorial/configure07.png)  

1. A **készlet kiválasztása** területen adja meg az **új létrehozása** elemet (másik lehetőségként válasszon egy már létező készletet). Hagyja, hogy a többi érték legyen alapértelmezett.    A készlet-kiszolgálók területen írja be az IP-címet az **IP-cím/csomópont neve** mezőbe. A **portot** határozza meg. Kattintson a **mentés & tovább** gombra.
 
    ![Képernyőkép, amely megjeleníti a "készlet tulajdonságai" lapot az "IP-cím/csomópont neve" és a "Port" szövegmezőben, valamint a "Mentés & tovább" gombot.](./media/kerbf5-tutorial/configure08.png)

1. Az egyszeres Sign-On beállítások képernyőn válassza az **egyszeri bejelentkezés engedélyezése** lehetőséget. A **kiválasztott egyszeri Sign-On típusnál** válassza a **Kerberos** lehetőséget. Cserélje le a **Session. SAML. Last. Identity**  nevű munkamenetet. **SAML. Last. attr. name. Identity** nevet a **Felhasználónév forrása** mezőben (ez a változó az Azure ad-ben a jogcímek leképezésével van beállítva). Válassza a **Speciális beállítások megjelenítése** lehetőséget. A **Kerberos** tartomány mezőbe írja be a tartománynevet. A **fiók neve/fiók jelszava** területen adja meg az APM-delegálási fiókot és a jelszót. A **KDC** mezőben adhatja meg a tartományvezérlő IP-címét. Kattintson a **mentés & tovább** gombra.

    ![Képernyőfelvétel: az "egyszeres Sign-On beállítások" szöveggel kiemelve és a "Mentés & következő" gomb kiválasztásával.](./media/kerbf5-tutorial/configure09.png)   

1. Ebben az útmutatóban kihagyjuk a végponti ellenőrzéseket.  További részletekért tekintse meg az F5 dokumentációját.  A képernyőn válassza a **mentés & tovább** lehetőséget.

    ![Képernyőfelvétel: a "végpont-ellenőrzések tulajdonságai" lap és a "Mentés & következő" gomb kijelölése.](./media/kerbf5-tutorial/configure10.png) 

1. Fogadja el az alapértelmezett beállításokat, majd kattintson a **mentés & tovább** gombra. Az SAML-munkamenet-kezelési beállításokkal kapcsolatos részletekért olvassa el az F5 dokumentációját.


    ![A "timeout Settings" (időtúllépési beállítások) lapot megjelenítő képernyőkép, amely a "Mentés & következő" gombra kattint.](./media/kerbf5-tutorial/configure11.png) 
 
1. Tekintse át az összegző képernyőt, és válassza a **telepítés** lehetőséget a Big-IP konfigurálásához.
 
    ![Képernyőfelvétel: "az alkalmazás készen áll a központi telepítésre, és az" összefoglalás "szakasz és a" telepítés "gomb van kiválasztva.](./media/kerbf5-tutorial/configure12.png)

1. Az alkalmazás konfigurálását követően kattintson a **Befejezés** gombra.

    ![Képernyőfelvétel: "az alkalmazás üzembe helyezése" lap, amelyen a "Finish" (Befejezés) gomb van kiválasztva.](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Speciális konfiguráció

>[!NOTE]
>A hivatkozáshoz kattintson [ide](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>Active Directory AAA-kiszolgáló konfigurálása

A hozzáférés-vezérlési kezelőben (APM) Active Directory AAA-kiszolgálót kell konfigurálnia a felhasználók hitelesítéséhez használandó APM-EK és hitelesítő adatok megadásához.

1. A fő lapon kattintson a **hozzáférési házirend > AAA-kiszolgálók > Active Directory** elemre. Megnyílik a Active Directory Servers List képernyő.

2. Kattintson a **Létrehozás** lehetőségre. Megnyílik az új kiszolgáló tulajdonságai képernyő.

3. A **név** mezőbe írjon be egy egyedi nevet a hitelesítési kiszolgálónak.

4. A **tartomány neve** mezőbe írja be a Windows-tartomány nevét.

5. A **Kiszolgálói kapcsolatok** beállításnál válassza a következő lehetőségek egyikét:

   * Válassza a **készlet használata** lehetőséget az AAA-kiszolgáló magas rendelkezésre állásának beállításához.

   * Válassza a **közvetlen** lehetőséget az AAA-kiszolgáló önálló működéshez való beállításához.

6. Ha a **közvetlen** lehetőséget választotta, adjon meg egy nevet a **tartományvezérlő** mezőben.

7. Ha a **készlet** használata lehetőséget választotta, konfigurálja a készletet:

   * Írjon be egy nevet a **tartományvezérlő készletének neve** mezőbe.

   * Adja meg a készletben lévő **tartományvezérlőket** úgy, hogy mindegyikhez beírja az IP-címet és az állomásnevet, majd a **Hozzáadás** gombra kattint.

   * Az AAA-kiszolgáló állapotának figyeléséhez lehetősége van az állapotfigyelő kiválasztására: ebben az esetben csak az **gateway_icmp** figyelő szükséges. kiválaszthatja a **kiszolgálói készlet figyelője** listából.

8. A rendszergazda **neve** mezőbe írja be a kis-és nagybetűket megkülönböztető nevet egy olyan rendszergazdának, aki Active Directory rendszergazdai jogosultságokkal rendelkezik. Az APM az AD-lekérdezéshez tartozó **rendszergazdai név** és **rendszergazdai jelszó** mezőkben szereplő információkat használja. Ha a Active Directory névtelen lekérdezésekhez van konfigurálva, nem kell megadnia a Rendszergazdá nevét. Ellenkező esetben az APM-nek olyan fiókra van szüksége, amely megfelelő jogosultsággal rendelkezik a Active Directory-kiszolgálóhoz való kötéshez, a felhasználói csoport adatainak beolvasásához, valamint Active Directory jelszóházirend beolvasásához a jelszóval kapcsolatos funkciók támogatásához. (Az APM-nek be kell olvasnia a szabályzatokat, például ha bejelöli a jelszó módosításának kérése a felhasználótól az AD-lekérdezési művelet lejárata előtt beállítást.) Ha nem ad meg rendszergazdai fiókot ebben a konfigurációban, az APM a felhasználói fiók használatával kéri le az adatokat. Ez akkor működik, ha a felhasználói fióknak megfelelő jogosultsága van.

9. A **rendszergazdai jelszó** mezőbe írja be a tartománynévhez társított rendszergazdai jelszót.

10. A **rendszergazdai jelszó ellenőrzése** mezőben írja be újra a **tartománynév** beállításhoz társított rendszergazdai jelszót.

11. A **csoport gyorsítótárának élettartama** mezőben adja meg a napok számát. Az alapértelmezett élettartam 30 nap.

12. A **jelszó biztonsági objektum gyorsítótárának élettartama** mezőben adja meg a napok számát. Az alapértelmezett élettartam 30 nap.

13. A **Kerberos előhitelesítés titkosítási típusa** listából válassza ki a titkosítási típust. Az alapértelmezett érték a **none**. Ha titkosítási típust ad meg, a BIG-IP rendszer az első hitelesítési szolgáltatási kérelem (AS-REQ) csomagján belül Kerberos-előhitelesítést is tartalmaz.

14. Az **időtúllépés** mezőben adja meg az AAA-kiszolgáló időtúllépési intervallumát (másodpercben). (Ez a beállítás nem kötelező.)

15. Kattintson a **kész** gombra. Az új kiszolgáló megjelenik a listán. Ezzel hozzáadja az új Active Directory-kiszolgálót a Active Directory kiszolgálók listájához.

    ![Képernyőkép, amely az "általános tulajdonságok" és a "konfiguráció" szakaszt jeleníti meg.](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML-konfiguráció

1. Importálnia kell a metaadat-tanúsítványt az F5 rendszerbe, amelyet később a telepítési folyamat során fog használni. Navigáljon a **System > tanúsítványkezelő > adatforgalom-tanúsítvány kezelése > SSL-tanúsítvány listára**. Válassza az **Importálás** lehetőséget a jobb oldali sarokban.

    ![Képernyőfelvétel: az "Importálás" gomb kiválasztásával megjelenik az "import S S L tanúsítvány/kulcs forrása" oldal.](./media/kerbf5-tutorial/configure18.png)

2. Az SAML-IDENTITÁSSZOLGÁLTATÓ beállításához **navigáljon a > összevonási > SAML: szolgáltató > külső identitásszolgáltató-összekötők** lehetőségre, majd kattintson **a > létrehozása metaadatokból** elemre.

    ![A "létrehozás" legördülő menüből kiválasztott "a metaadatokból" nevű, "S" M szolgáltatót megjelenítő képernyő.](./media/kerbf5-tutorial/configure19.png)

    ![Képernyőfelvétel: az "új S A M L I d P-összekötő létrehozása" párbeszédpanel megjelenítése.](./media/kerbf5-tutorial/configure20.png)

    ![Képernyőkép: a "General Settings" (általános beállítások) beállítású "M L I d o-összekötő" ablak megjelenítése.](./media/kerbf5-tutorial/configure21.png)

    ![Képernyőfelvétel: az "egyszeri bejelentkezés szolgáltatás beállításai" beállítással megjelenítheti az "M L I d o-összekötő szerkesztése" ablakot.](./media/kerbf5-tutorial/configure22.png)

    ![A "biztonsági beállítások" lehetőség kiválasztásával megjelenő képernyőkép, amely megjeleníti a](./media/kerbf5-tutorial/configure23.png)

    ![Képernyőfelvétel: az "S l I d I d P-összekötő" ablak "S L O Service Settings" beállítással van kiválasztva.](./media/kerbf5-tutorial/configure24.png)

1. Az SAML SP beállításához navigáljon a **hozzáférés > összevonási > SAML-szolgáltató > helyi SP-szolgáltatások** elemre, majd kattintson a **Létrehozás** gombra. Hajtsa végre a következő információkat, majd kattintson **az OK** gombra.

    * Típus neve: KerbApp200SAML
    * Entitás azonosítója *: https://kerb-app.com.cutestat.com
    * SP-név beállításai
    * Séma: https
    * Gazdagép: kerbapp200. redemo. Live
    * Leírás: kerbapp200. redemo. Live

     ![Képernyőkép, amely a "General Settings" (általános beállítások) beállítással jeleníti meg az "M L S P szolgáltatás szerkesztése" ablakot.](./media/kerbf5-tutorial/configure25.png)

     b. Válassza ki az SP-konfigurációt, a KerbApp200SAML, majd kattintson a **identitásszolgáltató-összekötők kötése/** megszüntetése elemre.

     ![Képernyőkép, amely az "S M L Service Provider – local S P Services" lapot jeleníti meg a "KerbAPP200 S A M L" beállítással.](./media/kerbf5-tutorial/configure26.png)

     ![A "bind/szüntesse I d P-összekötők" gomb kiválasztását bemutató képernyőkép.](./media/kerbf5-tutorial/configure27.png)

     c. Kattintson az **új sor hozzáadása** elemre, és válassza ki az előző lépésben létrehozott **külső identitásszolgáltató-összekötőt** , kattintson a **frissítés** elemre, majd **az OK** gombra.

     ![Az "új sor hozzáadása" gomb kiválasztásával megjelenő "a s L I d I d PS-t használó, az" új sor hozzáadása "gombot megjelenítő képernyőkép.](./media/kerbf5-tutorial/configure28.png)

1. A Kerberos SSO konfigurálásához navigáljon az **> egyszeri bejelentkezés > Kerberos**, teljes információ és kattintson a **kész** lehetőségre.

    >[!Note]
    > Ehhez létre kell hoznia és meg kell adni a Kerberos-delegálási fiókot. Tekintse át a KCD szakaszt (lásd: függelék a változó hivatkozásokhoz)

    * **Username forrás**: Session. SAML. Last. attr. name. http: \/ /schemas.xmlSOAP.org/ws/2005/05/Identity/Claims/givenName

    * **Felhasználói tartomány forrása**: Session. Logon. Last. domain

        ![Képernyőkép, amely Sign-On a "username Source" és a "felhasználói tartomány forrása" szövegmezőket jeleníti meg kiemelve.](./media/kerbf5-tutorial/configure29.png)

1. A hozzáférési profil konfigurálásához navigáljon a hozzáférés **> profil/szabályzatok > hozzáférési profil (munkamenet-szabályzatok)** elemre, kattintson a **Létrehozás** gombra, végezze el a következő információkat, majd kattintson a **kész** gombra.

    * Név: KerbApp200
    * Profil típusa: ALL
    * Profil hatóköre: profil
    * Nyelvek: angol

        ![Képernyőfelvétel: a "Name", "Profil típusa" és "nyelvi beállítások" szövegmezőket tartalmazó "profilok/házirendek – tulajdonságok" oldal.](./media/kerbf5-tutorial/configure30.png)

1. Kattintson a névre, KerbApp200, végezze el a következő információkat, majd kattintson a **frissítés** gombra.

    * Tartományi cookie: főbemutató. élő
    * SSO-konfiguráció: KerAppSSO_sso

        ![Képernyőkép, amely a "tartomány cookie" szövegmezővel és a "S S O-konfigurációval" legördülő menüből és a "frissítés" gomb kiválasztásával jeleníti meg a "S S D/Auth Domains" lapot.](./media/kerbf5-tutorial/configure31.png)

1. Kattintson a **hozzáférési házirend** elemre, majd kattintson a **hozzáférési szabályzat szerkesztése** elemre a "KerbApp200" profilhoz.

    ![Képernyőkép a "hozzáférési szabályzat" oldaláról, amelyen a "hozzáférési szabályzat szerkesztése a profil KerbApp200" művelet be van jelölve.](./media/kerbf5-tutorial/configure32.png)

    ![A "hozzáférési szabályzat" oldalt és az "S A M L Authentication S P" párbeszédpanelt megjelenítő képernyőkép.](./media/kerbf5-tutorial/configure33.png)

    ![A "hozzáférési szabályzat" oldalt és a "változó hozzárendelése" párbeszédpanelt a "hozzárendelés" szövegmezők kiemelésével ábrázoló képernyőkép.](./media/kerbf5-tutorial/configure34.png)

    * **Session. Logon. Last. usernameUPN kifejezés {[mcget {Session. SAML. Last. Identity}]}**

    * **Session. ad. lastactualdomain TEXT-bemutató. élő**

        ![Képernyőfelvétel: a "hozzáférési szabályzat" és a "Active Directory" párbeszédpanel, amely a "SearchFilter" szövegmezőt mutatja.](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName =% {Session. Logon. Last. usernameUPN})**

        ![Képernyőkép, amely az "A D lekérdezés – ág szabályok" párbeszédpanellel jeleníti meg a "hozzáférési szabályzat" lapot.](./media/kerbf5-tutorial/configure36.png)

        ![Képernyőkép, amely az "egyéni változó" és az "egyéni kifejezés" szövegmezőket jeleníti meg.](./media/kerbf5-tutorial/configure37.png)

    * **Session. Logon. Last. username kifejezés {"[mcget {Session. ad. Last. attr. sAMAccountName}]"}**

        ![Képernyőkép, amely a "username from login Page" szövegmezőt jeleníti meg.](./media/kerbf5-tutorial/configure38.png)

    * **mcget {Session. Logon. Last. username}**
    * **mcget {Session. Logon. Last. password**

1. Új csomópont hozzáadásához navigáljon a **helyi forgalom > csomópontok > csomópont listára, kattintson a Létrehozás gombra**, hajtsa végre a következő információkat, majd kattintson a **kész** gombra.

    * Név: KerbApp200
    * Leírás: KerbApp200
    * Címe: 192.168.20.200

        ![Képernyőkép, amely az "új csomópont" oldalt jeleníti meg a "név", a "Description" és a "címek" szövegmezőket kiemelve, a "befejezett" gombot pedig kiválasztva.](./media/kerbf5-tutorial/configure39.png)

1. Új készlet létrehozásához navigáljon a **helyi forgalom > készletek > készlet listára, kattintson a Létrehozás gombra**, hajtsa végre a következő információkat, majd kattintson a **kész** gombra.

    * Név: KerbApp200-Pool
    * Leírás: KerbApp200-Pool
    * Állapot-figyelők: http
    * Címe: 192.168.20.200
    * Szolgáltatási port: 81

        ![Képernyőfelvétel: az "új készlet" oldal és a megadott értékek, valamint a "befejezett" gomb van kiválasztva.](./media/kerbf5-tutorial/configure40.png)

1. Virtuális kiszolgáló létrehozásához navigáljon a **helyi forgalom > virtuális kiszolgálók > Virtual Server List > +**, végezze el a következő információkat, majd kattintson a **kész** gombra.

    * Név: KerbApp200
    * Cél címe/maszk: host 192.168.30.200
    * Szolgáltatási port: Port 443 HTTPS
    * Hozzáférési profil: KerbApp200
    * Az előző lépésben létrehozott hozzáférési profil megadása

        ![Képernyőkép, amely a "név", a "cél címe/maszk" és a "Service port" szövegmezőket jeleníti meg.](./media/kerbf5-tutorial/configure41.png)

        ![A "virtuális kiszolgálók listája" oldalt megjelenítő képernyőkép, amely a "hozzáférési profil" legördülő menüvel jelenik meg.](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>A Kerberos-delegálás beállítása 

>[!NOTE]
>A hivatkozáshoz kattintson [ide](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **1. lépés:** Delegálási fiók létrehozása

    **Példa**
    * Tartománynév: **főbemutató. élő**

    * Sam-fiók neve: **Big-ipuser**

    * New-ADUser neve "APM delegálási fiók"-UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -sAMAccountName "Big-ipuser"-PasswordNeverExpires $True-Enabled $True-AccountPassword (read-Host-AsSecureString "password! 1234")

* **2. lépés:** SPN beállítása (az APM delegálási fiókján)

    **Példa**
    * Setspn – A **Host/Big-ipuser. redemo. Live** Big-ipuser

* **3. lépés:** SPN-delegálás (a App Service fiók esetében) állítsa be a megfelelő delegálást az F5 delegálási fiókhoz.
    Az alábbi példában az APM delegálási fiók konfigurálva van a KCD for FRP-App1. redemo használatára. élő alkalmazás.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure43.png)

* Adja [meg a fenti](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)hivatkozási dokumentumban említett részleteket.

### <a name="create-f5-test-user"></a>F5 test User létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az F5 billentyűvel. A felhasználók az F5 platformon való hozzáadásához az [F5 ügyfél-támogatási csapat](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) használható. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen az F5 csempére kattint, automatikusan be kell jelentkeznie az F5-be, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Az F5 kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Az F5 egyszeri bejelentkezés konfigurálása a fejléc-alapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezés konfigurálása a speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

- [F5 BIG-IP APM és Azure AD-integráció a biztonságos hibrid hozzáféréshez](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [Oktatóanyag az F5 BIG-IP Virtual Edition virtuális gép üzembe helyezéséhez az Azure IaaS a biztonságos hibrid hozzáférés érdekében](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-bigip-deployment-guide)

- [Oktatóanyag Azure Active Directory egyszeri bejelentkezéses integrációhoz az F5 BIG-IP jelszó nélküli VPN-hez](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)
