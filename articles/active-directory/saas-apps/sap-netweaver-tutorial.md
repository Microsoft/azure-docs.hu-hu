---
title: 'Oktatóanyag: oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP NetWeaver-vel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP NetWeaver között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: b6b8dab3472473082562f1e4c0216886191e4a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97962805"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP NetWeaver-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP NetWeavert Azure Active Directory (Azure AD) használatával. Az SAP NetWeaver és az Azure AD integrálásával a következőket teheti:

* Az SAP NetWeaver-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SAP NetWeaverba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SAP NetWeaver egyszeri bejelentkezés (SSO) engedélyezve előfizetés.
* SAP NetWeaver V 7.20 szükséges atleast

## <a name="scenario-description"></a>Forgatókönyv leírása

* Az SAP NetWeaver az **SAML** (**SP által kezdeményezett SSO**) és a **OAuth** egyaránt támogatja. Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. 

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

> [!NOTE]
> Konfigurálja az alkalmazást az SAML-ban vagy a OAuth-ben a szervezeti követelményeknek megfelelően. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>SAP NetWeaver hozzáadása a katalógusból

Az SAP NetWeaver Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP NetWeaver-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **SAP NetWeaver** kifejezést.
1. Válassza az **SAP NetWeaver** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-sap-netweaver"></a>Azure AD SSO konfigurálása és tesztelése az SAP NetWeaver-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az SAP NetWeaver használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és az SAP NetWeaver kapcsolódó felhasználója között.

Az Azure AD SSO és az SAP NetWeaver konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja az SAP NetWeaver](#configure-sap-netweaver-using-saml)** -t az SAML használatával az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre egy SAP NetWeaver-teszt felhasználót](#create-sap-netweaver-test-user)** , hogy az az SAP NetWeaver-ban található B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.
1. A **[OAUTH SAP NetWeaver konfigurálása](#configure-sap-netweaver-for-oauth)** a OAuth beállításainak konfigurálásához az alkalmazás oldalán.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az SAP NetWeaver használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az SAP NetWeaver vállalati webhelyre rendszergazdaként

1. Győződjön meg arról, hogy a **http** -és **https** -szolgáltatások aktívak, és a megfelelő portok a **SMICM** T-Code-ban vannak hozzárendelve.

1. Jelentkezzen be az SAP System (T01) üzleti ügyfelére, ahol az SSO szükséges, és aktiválja a HTTP biztonsági munkamenetek felügyeletét.

    a. Nyissa meg a tranzakciós kódot **SICF_SESSIONS**. Megjeleníti az aktuális értékekkel rendelkező összes releváns profil paramétert. A következőhöz hasonlóak:
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Módosítsa a fenti paramétereket a szervezet követelményeinek megfelelően, a fenti paramétereket csak jelzésként adja meg.

    b. Ha szükséges, módosítsa a paramétereket az SAP rendszer példány/alapértelmezett profiljában, majd indítsa újra az SAP rendszert.

    c. A HTTP biztonsági munkamenet engedélyezéséhez kattintson duplán a megfelelő ügyfélre.

    ![A HTTP biztonsági munkamenet ](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktiválja az alábbi SICF-szolgáltatásokat:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Nyissa meg az SAP System [T01/122] üzleti ügyfelének tranzakciós kód **egy saml2** . Megnyílik egy felhasználói felület egy böngészőben. Ebben a példában a 122-as SAP üzleti ügyfélként feltételezzük.

    ![Tranzakció kódja](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Adja meg felhasználónevét és jelszavát a felhasználói felületen való belépéshez, majd kattintson a **Szerkesztés** gombra.

    ![Felhasználónév és jelszó](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Cserélje le a **szolgáltató nevét** a T01122 `http://T01122` elemre, majd kattintson a **Save (Mentés**) gombra.

    > [!NOTE]
    > Alapértelmezés szerint a szolgáltató neve `<sid><client>` formátumként, de az Azure ad a nevét a következő formátumban adja meg:. a `<protocol>://<name>` szolgáltató nevének fenntartása úgy történik, hogy `https://<sid><client>` több SAP NetWeaver ABAP-motor legyen KONFIGURÁLVA az Azure ad-ben.

    ![Több SAP NetWeaver ABAP motor](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Szolgáltatói metaadatok generálása**: – Ha elkészült a **helyi szolgáltató** és a **megbízható szolgáltatók** beállításainak konfigurálása az SAML 2,0 felhasználói felületen, a következő lépés a szolgáltató metaadat-fájljának létrehozása (amely tartalmazza az összes beállítást, a hitelesítési környezeteket és az SAP más konfigurációit). A fájl létrehozása után fel kell töltenie ezt az Azure AD-ben.

    ![Szolgáltatói metaadatok generálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Lépjen a **helyi szolgáltató lapra**.

    b. Kattintson a **metaadatok** elemre.

    c. Mentse a generált **metaadatokat tartalmazó XML-fájlt** a számítógépén, és töltse fel az **SAML alapszintű konfigurációs** szakaszba az **azonosító** és a **Válasz URL-címének** automatikus feltöltéséhez Azure Portalban.

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **SAP NetWeaver** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    a. Kattintson a **metaadatok feltöltése** gombra a **szolgáltatói metaadat fájljának** feltöltéséhez, amelyet korábban kapott.

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

    c. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-** értékei automatikusan feltöltve lesznek az **alapszintű SAML-konfigurációs** szakasz szövegmezőben az alábbi ábrán látható módon:

    d. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Láttuk, hogy néhány ügyfelünk hibát jelzett a példányhoz konfigurált helytelen válasz URL-cím miatt. Ha ilyen hibaüzenetet kap, a következő PowerShell-parancsfájl használatával megadhatja a példány helyes válaszának URL-címét.
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > Az ServicePrincipal objektum AZONOSÍTÓját elsőként kell beállítani, vagy itt is átadhatja.

1. Az SAP NetWeaver alkalmazás meghatározott formátumban várja az SAML-kijelentéseket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![Notes szerkesztése](common/edit-attribute.png)

1. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    a. Kattintson a **Szerkesztés ikonra** a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Szerkesztés ikon](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Az **átalakítás** listából válassza a **ExtractMailPrefix ()** elemet.

    c. Az **1. paraméter** listából válassza a **User. userPrincipalName** elemet.

    d. Kattintson a **Mentés** gombra.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **SAP NetWeaver beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát az SAP NetWeaver elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **SAP NetWeaver** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sap-netweaver-using-saml"></a>Az SAP NetWeaver konfigurálása az SAML használatával

1. Jelentkezzen be az SAP System szolgáltatásba, és lépjen a tranzakciós kód egy SAML2. Ekkor megnyílik az SAML konfigurációs képernyővel rendelkező új böngészőablak.

2. A megbízható identitás-szolgáltató (Azure AD) végpontjának konfigurálásához nyissa meg a **megbízható szolgáltatók** lapot.

    ![Önálló Sign-On megbízható szolgáltatók konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Kattintson a **Hozzáadás** gombra, és válassza a **metaadatok feltöltése** elemet a helyi menüből.

    ![Egyetlen Sign-On konfigurálása 2](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Töltse fel a metaadat-fájlt, amelyet a Azure Portal letöltött.

    ![Egy Sign-On 3 konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. A következő képernyőn írja be az alias nevét. Például aadsts, majd a folytatáshoz kattintson a **tovább** gombra.

    ![Egy Sign-On 4 konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Győződjön meg arról, hogy a **kivonatoló algoritmusnak** **SHA-256** értékűnek kell lennie, és nem kell módosítania, majd nyomja meg a **tovább** gombot.

    ![Egy Sign-On 5 konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. **Egy Sign-On végponton** használja a **http post** elemet, majd a folytatáshoz kattintson a **tovább** gombra.

    ![Egy Sign-On 6 konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Az **egyszeri kijelentkezési végpontokon** válassza a **HTTPRedirect** lehetőséget, majd a folytatáshoz kattintson a **tovább** gombra.

    ![Egy Sign-On 7 konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Az összetevő- **végpontokon** kattintson **a Tovább gombra a** folytatáshoz.

    ![Egy Sign-On 8 konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. A **hitelesítési követelmények** területen kattintson a **Befejezés** gombra.

    ![Egy Sign-On 9 konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Ugrás a lapon a **megbízható szolgáltató**  >  **identitás-összevonása** elemre (a képernyő aljáról). Kattintson a **Szerkesztés** gombra.

    ![Egy Sign-On 10 konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Kattintson a **Hozzáadás** elemre az **identitás-összevonás** lapon (alsó ablak).

    ![Egyetlen Sign-On 11 konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Az előugró ablakban válassza a nem **meghatározott** lehetőséget a **támogatott NameID-formátumok** közül, majd kattintson az OK gombra.

    ![Egy Sign-On 12 konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

1. Adja meg a **felhasználói azonosító forrás** értékét **az érvényesítési attribútumként**, a **felhasználói azonosító megfeleltetési módjának** értékeként az **e-mail-cím** és az **érvényesítési attribútum nevét** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    ![Egyetlen Sign-On konfigurálása ](./media/sapnetweaver-tutorial/nameid-format.png)

14. Vegye figyelembe, hogy a **felhasználói azonosító forrása** és a **felhasználói azonosító megfeleltetési módjának** értéke határozza meg az SAP-felhasználó és az Azure ad-jogcím közötti kapcsolatot.

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Forgatókönyv: az SAP-felhasználó Azure AD-felhasználó általi leképezése.

    a. NameID részletei – képernyőfelvétel az SAP-ból.

    ![Egyetlen Sign-On 13 konfigurálása](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Képernyőkép az Azure AD-ben szükséges jogcímek megemlítéséről.

    ![Egy Sign-On 14 konfigurálása](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Forgatókönyv: az SAP felhasználói azonosító kiválasztása a konfigurált e-mail-cím alapján a SU01-ben. Ebben az esetben az e-mail-azonosítót a su01-ben kell konfigurálni minden olyan felhasználó esetében, aki egyszeri bejelentkezést igényel.

    a.  NameID részletei – képernyőfelvétel az SAP-ból.

    ![Egyetlen Sign-On konfigurálása 15](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. képernyőkép az Azure AD-ben szükséges jogcímek megemlítéséről.

    ![Egy Sign-On 16 konfigurálása](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Kattintson a **Mentés** elemre, majd az **Engedélyezés** lehetőségre az Identitáskezelés engedélyezéséhez.

    ![Egy Sign-On 17 konfigurálása](./media/sapnetweaver-tutorial/configuration1.png)

16. Ha a rendszer kéri, kattintson **az OK** gombra.

    ![Egy Sign-On 18 konfigurálása](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>SAP NetWeaver-teszt felhasználó létrehozása

    Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az SAP NetWeaver-ben. Vegye fel a kapcsolatot a házon belüli SAP szakértői csapatával, vagy működjön együtt a szervezet SAP-partnerével, és vegye fel a felhasználókat az SAP NetWeaver platformon.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

1. Miután aktiválta az Azure AD-szolgáltatót, próbáljon meg hozzáférni az alábbi URL-címhez az egyszeri bejelentkezéshez (a Felhasználónév & jelszava nem jelenik meg)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (vagy) használja az alábbi URL-címet

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Cserélje le a sapurl-t a tényleges SAP állomásnévvel.

2. A fenti URL-címnek az alább említett képernyőre kell mutatnia. Ha elérheti az alábbi oldalt, az Azure AD SSO telepítése sikeresen befejeződött.

    ![Egyetlen Sign-On tesztelése](./media/sapnetweaver-tutorial/testingsso.png)

3. Ha a Felhasználónév & a jelszó kérése, akkor a probléma diagnosztizálásához engedélyezze a nyomkövetést az alábbi URL-cím használatával

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Az OAuth SAP NetWeaver konfigurálása

1. Az SAP-dokumentált folyamat a következő helyen érhető el: [NetWeaver Gateway Service engedélyezése és OAuth 2,0 hatókör létrehozása](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Nyissa meg a SPRO, és keresse meg az **aktiválási és karbantartási szolgáltatásokat**.

    ![Szolgáltatások aktiválása és karbantartása](./media/sapnetweaver-tutorial/oauth01.png)

3. Ebben a példában szeretnénk csatlakozni a OData szolgáltatáshoz: `DAAG_MNGGRP` a OAuth és az Azure ad SSO használatával. A műszaki szolgáltatás neve keresse meg a szolgáltatást `DAAG_MNGGRP` , és aktiválja, ha még nem aktív, már (keresse meg az `green` állapotot az ICF-csomópontok lapon). Győződjön meg arról, hogy a rendszeralias (a csatlakoztatott háttérrendszer, ahol a szolgáltatás ténylegesen fut) helyes.

    ![OData szolgáltatás](./media/sapnetweaver-tutorial/oauth02.png)

    * Ezután kattintson a felső gomb sávján a nyomógomb **OAuth** elemre, és rendelje hozzá a `scope` (alapértelmezett név megtartása az elérhetőként) lehetőséget.

4. Példánkban a hatókört a `DAAG_MNGGRP_001` szolgáltatás neve alapján hozza létre a rendszer automatikusan egy szám hozzáadásával. A jelentés használatával `/IWFND/R_OAUTH_SCOPES` módosíthatja a hatókör nevét, vagy manuálisan is létrehozhatja azt.

    ![OAuth konfigurálása](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Üzenet `soft state status is not supported` – figyelmen kívül hagyható, mert nincs probléma. További részletekért [tekintse](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)meg a következő témakört:.

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Szolgáltatás felhasználójának létrehozása a OAuth 2,0-ügyfél számára

1. A OAuth2 a `service ID` használatával szerezheti be a végfelhasználó hozzáférési jogkivonatát az nevében. Fontos korlátozás a OAuth-kialakítással: a- `OAuth 2.0 Client ID` nek meg kell egyeznie a `username` OAuth 2,0 ügyfél által a hozzáférési jogkivonat kérésekor való bejelentkezéskor használt értékkel. Ezért a példánkban az ÜGYFÉL1 nevű OAuth 2,0-ügyfelet fogjuk regisztrálni, és az előfeltétel, hogy az azonos nevű (ÜGYFÉL1) felhasználónak léteznie kell az SAP-rendszeren, és hogy a felhasználó az említett alkalmazás által használt konfigurációt fogja használni. 

2. OAuth-ügyfél regisztrálásakor a `SAML Bearer Grant type` .

    >[!NOTE]
    >További részletekért tekintse meg a OAuth 2,0 ügyfél regisztrációját az SAML-tulajdonos [engedélyezési típusához](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type).

3. tcod: SU01/hozza létre az ÜGYFÉL1 felhasználót, `System type` és rendelje hozzá a jelszót, mentse a hitelesítő adatokat az API-programozó számára, akinek a felhasználónevével kell írnia a felhasználónevet. Nincs hozzárendelve profil vagy szerepkör.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Regisztrálja az új OAuth 2,0 ügyfél-azonosítót a létrehozás varázslóval

1. Új **OAuth 2,0 ügyfél** -indítási tranzakció **SOAUTH2** regisztrálása. A tranzakció áttekintést nyújt a már regisztrált OAuth 2,0-ügyfelekről. Válassza a **Létrehozás** lehetőséget, hogy elindítsa a varázslót az új OAuth-ügyfél nevű CLIENT1in ebben a példában.

2. Nyissa meg a T-code: **SOAUTH2** , és adja meg a leírást, majd kattintson a **tovább** gombra.

    ![SOAUTH2](./media/sapnetweaver-tutorial/oauth04.png)

    ![OAuth 2,0 ügyfél-azonosító](./media/sapnetweaver-tutorial/oauth05.png)

3. Válassza ki a már hozzáadott **egy saml2 identitásszolgáltató – Azure ad** elemet a legördülő listából, és mentse.

    ![EGY SAML2 identitásszolgáltató – Azure AD 1](./media/sapnetweaver-tutorial/oauth06.png)

    ![EGY SAML2 identitásszolgáltató – Azure AD 2](./media/sapnetweaver-tutorial/oauth07.png)

    ![EGY SAML2 identitásszolgáltató – Azure AD 3](./media/sapnetweaver-tutorial/oauth08.png)

4. A korábban létrehozott hatókör hozzáadásához kattintson a **Hozzáadás** a hatókör-hozzárendelés alatt lehetőségre: `DAAG_MNGGRP_001`

    ![Hatókör](./media/sapnetweaver-tutorial/oauth09.png)

    ![hatókör-hozzárendelés](./media/sapnetweaver-tutorial/oauth10.png)

5. Kattintson a **Befejezés** gombra.

## <a name="next-steps"></a>Következő lépések

Az Azure AD SAP NetWeaver konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
