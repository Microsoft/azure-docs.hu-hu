---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses integráció a Citrix ADC-vel (fejléc-alapú hitelesítéssel) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést (SSO) a Azure Active Directory és a Citrix ADC között a fejléc-alapú hitelesítés használatával.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 9cab0597aeb3bc28f391de558240e5d894f5a49c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735231"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-header-based-authentication"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses integráció a Citrix ADC-vel (fejléc-alapú hitelesítés)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Citrix ADC-t Azure Active Directory (Azure AD) használatával. Ha a Citrix ADC-t az Azure AD-vel integrálja, a következőket teheti:

* Szabályozza az Azure AD-t, aki hozzáfér a Citrix ADC-hez.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Citrix ADC-be az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Citrix ADC egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. Az oktatóanyag a következő forgatókönyveket tartalmazza:

* **SP – kezdeményezett** Egyszeri bejelentkezés a Citrix ADC-hez

* Felhasználói üzembe helyezés a Citrix ADC **-** hez

* [Fejléc-alapú hitelesítés Citrix ADC esetén](#publish-the-web-server)

* [Kerberos-alapú hitelesítés Citrix ADC esetén](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-adc-from-the-gallery"></a>Citrix ADC hozzáadása a katalógusból

A Citrix ADC Azure AD-val való integrálásához először adja hozzá a Citrix ADC-t a felügyelt SaaS-alkalmazások listájához a gyűjteményből:

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.

1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.

1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.

1. A **Hozzáadás a** katalógusból szakaszban adja meg a **Citrix ADC** kifejezést a keresőmezőbe.

1. Az eredmények között válassza ki a **CITRIX ADC** elemet, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc"></a>Azure AD SSO konfigurálása és tesztelése a Citrix ADC-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Citrix ADC használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Citrix ADC-ben.

Az Azure AD SSO Citrix ADC-vel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az [Azure ad SSO konfigurálása](#configure-azure-ad-sso) – a funkció használatának engedélyezése a felhasználók számára.

    1. [Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user) – az Azure ad SSO teszteléséhez B. Simon használatával.

    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) – hogy a B. Simon engedélyezze az Azure ad SSO használatát.

1. [CITRIX ADC SSO konfigurálása](#configure-citrix-adc-sso) – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.

    * [Hozzon létre egy CITRIX ADC-tesztet használó felhasználót](#create-a-citrix-adc-test-user) – a felhasználó Azure ad-képviseletéhez csatolt B. Simon-ügyfélként a Citrix ADC-ben.

1. [SSO tesztelése](#test-sso) – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ha az Azure AD SSO-t a Azure Portal használatával szeretné engedélyezni, hajtsa végre a következő lépéseket:

1. A Azure Portal a **CITRIX ADC** Application Integration panel **kezelés** területén válassza az **egyszeri bejelentkezés** lehetőséget.

1. Az **egyszeri bejelentkezési módszer kiválasztása** panelen válassza az **SAML** lehetőséget.

1. Az **egyszeri Sign-On beállítása az SAML-vel** panelen válassza a toll **szerkesztése** ikont az **alapszintű SAML-konfigurációhoz** a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás **identitásszolgáltató-kezdeményezésű** módban való konfigurálásához:

    1. Az **azonosító** szövegmezőben adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<Your FQDN>`

    1. A **Válasz URL-címe** szövegmezőben adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Az alkalmazás **SP-kezdeményezésű** módban való konfigurálásához válassza a **további URL-címek beállítása** lehetőséget, és végezze el a következő lépést:

    * A **bejelentkezési URL** szövegmezőben adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Az ebben a szakaszban használt URL-címek nem valós értékek. Frissítse ezeket az értékeket az azonosító, a válasz URL-cím és a bejelentkezési URL-cím tényleges értékeivel. Az értékek lekéréséhez lépjen kapcsolatba a [CITRIX ADC ügyfél-támogatási csapatával](https://www.citrix.com/contact/technical-support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.
    > * Az egyszeri bejelentkezés beállításához az URL-címeknek elérhetőnek kell lenniük a nyilvános webhelyekről. Engedélyeznie kell a tűzfalat vagy más biztonsági beállításokat a Citrix ADC oldalán ahhoz, hogy enble az Azure AD-t, hogy elküldhesse a tokent a konfigurált URL-címen.

1. Az **egyszeri Sign-On beállítása az SAML-vel** panelen az **SAML aláíró tanúsítvány** szakaszban az **alkalmazás-összevonás metaadatainak URL-címéhez** másolja ki az URL-címet, és mentse azt a Jegyzettömbben.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A Citrix ADC-alkalmazás az SAML-jogcímeket egy adott formátumban várja, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Válassza a **Szerkesztés** ikont, és módosítsa az attribútumok leképezéseit.

    ![Az SAML-attribútumok leképezésének szerkesztése](common/edit-attribute.png)

1. A Citrix ADC alkalmazás Emellett néhány további attribútumot is vár az SAML-válaszban való visszatéréshez. A **felhasználói attribútumok** párbeszédpanel felhasználói **jogcímek** területén a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumait a táblázatban látható módon:

    | Name | Forrás attribútum|
    | ---------------| --------------- |
    | mySecretID  | User. userPrincipalName |
    
    1. Válassza az **új jogcím hozzáadása** elemet a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    1. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    1. Hagyja üresen a **névteret** .

    1. **Attribútum** esetén válassza a **forrás** lehetőséget.

    1. A **forrás attribútum** listán adja meg az adott sorhoz megjelenített attribútumérték értékét.

    1. Kattintson az **OK** gombra.

    1. Kattintson a **Mentés** gombra.

1. A **CITRIX ADC beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali menüjében válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók** lehetőséget, majd válassza a **minden felhasználó** lehetőséget.

1. Válassza az **új felhasználó** elemet a panel tetején.

1. A **felhasználói** tulajdonságok területen hajtsa végre a következő lépéseket:

   1. A **név** mezőbe írja be a következőt: `B.Simon` .  

   1. A **Felhasználónév** mezőbe írja be a következőt: _username@companydomain.extension_ . Például: `B.Simon@contoso.com`.

   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel vagy másolja a **jelszóban** megjelenő értéket.

   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezheti a B. Simon felhasználó számára az Azure SSO használatát azáltal, hogy a felhasználónak hozzáférést biztosít a Citrix ADC-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.

1. Az alkalmazások listában válassza a **CITRIX ADC** elemet.

1. Az alkalmazás áttekintés területén a **kezelés** területen válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **Felhasználó hozzáadása** elemet. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a **felhasználók** listából. Válassza a **Kiválasztás** lehetőséget
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-citrix-adc-sso"></a>Citrix ADC SSO konfigurálása

Válasszon egy hivatkozást a konfigurálni kívánt hitelesítési típushoz:

- [Citrix ADC SSO konfigurálása fejléc-alapú hitelesítéshez](#publish-the-web-server)

- [Citrix ADC SSO konfigurálása Kerberos-alapú hitelesítéshez](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>A webkiszolgáló közzététele 

Virtuális kiszolgáló létrehozása:

1. Válassza a **Traffic Management**  >  **terheléselosztási**  >  **szolgáltatások** lehetőséget.
    
1. Válassza a **Hozzáadás** lehetőséget.

    ![Citrix ADC-konfiguráció – szolgáltatások ablaktábla](./media/header-citrix-netscaler-tutorial/web01.png)

1. Állítsa be az alábbi értékeket az alkalmazásokat futtató webkiszolgálóra:

   * **Szolgáltatás neve**
   * **Kiszolgáló IP-címe/meglévő kiszolgáló**
   * **Protokoll**
   * **Port**

     ![Citrix ADC konfigurációs panel](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>A terheléselosztó konfigurálása

A terheléselosztó konfigurálása:

1. Nyissa meg a **Traffic Management**  >  **terheléselosztási**  >  **virtuális kiszolgálókat**.

1. Válassza a **Hozzáadás** lehetőséget.

1. Állítsa be az alábbi értékeket a következő képernyőképen leírtak szerint:

    * **Név**
    * **Protokoll**
    * **IP-cím**
    * **Port**

1. Válassza az **OK** lehetőséget.

    ![Citrix ADC-konfiguráció – alapszintű beállítások panel](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>A virtuális kiszolgáló kötése

A terheléselosztó kötése a virtuális kiszolgálóval:

1. A **szolgáltatások és szolgáltatások csoport** ablaktáblán válassza a **nincs terheléselosztás virtuális kiszolgáló szolgáltatás kötése** elemet.

   ![Citrix ADC-konfiguráció – terheléselosztás a virtuális kiszolgáló szolgáltatás kötési paneljén](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Ellenőrizze a beállításokat az alábbi képernyőképen látható módon, majd kattintson a **Bezárás** gombra.

   ![Citrix ADC-konfiguráció – a Virtual Server Services-kötés ellenőrzése](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>A tanúsítvány kötése

A szolgáltatás TLS-ként történő közzétételéhez kösse a kiszolgálói tanúsítványt, majd tesztelje az alkalmazást:

1. A **tanúsítvány** területen válassza a **nincs kiszolgálói tanúsítvány** lehetőséget.

   ![Citrix ADC-konfiguráció – kiszolgálói tanúsítvány panel](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Ellenőrizze a beállításokat az alábbi képernyőképen látható módon, majd kattintson a **Bezárás** gombra.

   ![Citrix ADC-konfiguráció – a tanúsítvány ellenőrzése](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profil

A Citrix ADC SAML-profil konfigurálásához végezze el a következő szakaszt:

### <a name="create-an-authentication-policy"></a>Hitelesítési házirend létrehozása

Hitelesítési házirend létrehozása:

1. Válassza a **Biztonság**  >  **AAA – alkalmazás forgalmi**  >  **szabályzatok**  >  **hitelesítési**  >  **hitelesítési szabályzatok** lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget.

1. A **hitelesítési házirend létrehozása** panelen adja meg vagy válassza ki a következő értékeket:

    * **Név**: adja meg a hitelesítési házirend nevét.
    * **Művelet**: írja be az **SAML**-t, majd kattintson a **Hozzáadás** gombra.
    * **Kifejezés**: adja meg az **igaz** értéket.     
    
    ![Citrix ADC-konfiguráció – hitelesítési házirend létrehozása panel](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Válassza a **Létrehozás** lehetőséget.

### <a name="create-an-authentication-saml-server"></a>Hitelesítési SAML-kiszolgáló létrehozása

A hitelesítési SAML-kiszolgáló létrehozásához lépjen a **hitelesítés SAML-kiszolgáló létrehozása** panelre, majd hajtsa végre a következő lépéseket:

1. A **név** mezőben adja meg a hitelesítési SAML-kiszolgáló nevét.

1. Az **SAML-metaadatok exportálása** területen:

   1. Jelölje be a **Metaadatok importálása** jelölőnégyzetet.

   1. Adja meg az összevonási metaadatok URL-címét a korábban másolt Azure SAML felhasználói felületről.
    
1. A **kiállító neve** mezőbe írja be a megfelelő URL-címet.

1. Válassza a **Létrehozás** lehetőséget.

![Citrix ADC konfiguráció – hitelesítés SAML-kiszolgáló létrehozása panel](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Hitelesítési virtuális kiszolgáló létrehozása

Hitelesítési virtuális kiszolgáló létrehozása:

1.  Válassza a **Biztonság**  >  **AAA-alkalmazás forgalmi**  >  **szabályzatok**  >  **hitelesítési**  >  **hitelesítés virtuális kiszolgálók** lehetőséget.

1.  Válassza a **Hozzáadás** lehetőséget, majd hajtsa végre a következő lépéseket:

    1. A **név** mezőben adja meg a hitelesítési virtuális kiszolgáló nevét.

    1. Jelölje be a **nem címezhető** jelölőnégyzetet.

    1. A **protokoll** területen válassza az **SSL** lehetőséget.

    1. Válassza az **OK** lehetőséget.

    ![Citrix ADC-konfiguráció – hitelesítés virtuális kiszolgáló panel](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>A hitelesítési virtuális kiszolgáló konfigurálása az Azure AD használatára

Két rész módosítása a hitelesítési virtuális kiszolgálóhoz:

1.  A **speciális hitelesítési házirendek** panelen válassza a **nincs hitelesítési házirend** elemet.

    ![Citrix ADC-konfiguráció – speciális hitelesítési házirendek panel](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. A **házirend-kötés** ablaktáblán válassza ki a hitelesítési házirendet, majd válassza a **kötés** lehetőséget.

    ![Citrix ADC-konfiguráció – házirend kötési panelje](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. Az **űrlap alapú virtuális kiszolgálók** ablaktáblán válassza a **nincs terheléselosztás virtuális kiszolgáló** elemet.

    ![Citrix ADC konfiguráció – űrlap alapú virtuális kiszolgálók ablaktábla](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. A **hitelesítési FQDN** mezőben adjon meg egy teljes tartománynevet (FQDN) (kötelező).

1. Válassza ki azt a terheléselosztási virtuális kiszolgálót, amelyet az Azure AD-hitelesítéssel szeretne védelemmel ellátni.

1. Válassza a **kötés** lehetőséget.

    ![Citrix ADC-konfiguráció-terheléselosztás virtuális kiszolgáló kötésének panelje](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Ügyeljen rá, hogy a **hitelesítés virtuális kiszolgáló konfigurációja** ablaktáblán válassza a **kész** lehetőséget.

1. A módosítások ellenőrzéséhez a böngészőben nyissa meg az alkalmazás URL-címét. A korábban látott nem hitelesített hozzáférés helyett a bérlő bejelentkezési lapját kell látnia.

    ![Citrix ADC konfiguráció – bejelentkezési oldal egy böngészőben](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-sso-for-header-based-authentication"></a>Citrix ADC SSO konfigurálása fejléc-alapú hitelesítéshez

### <a name="configure-citrix-adc"></a>Citrix ADC konfigurálása

A Citrix ADC fejléc-alapú hitelesítéshez való konfigurálásához végezze el a következő szakaszt.

#### <a name="create-a-rewrite-action"></a>Újraírási művelet létrehozása

1. Ugrás a **AppExpert**-Újraírási  >    >  **műveletekre**.
 
    ![Citrix ADC konfiguráció-Újraírási műveletek ablaktábla](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Válassza a **Hozzáadás** lehetőséget, majd hajtsa végre a következő lépéseket:

    1. A **név** mezőben adja meg az Újraírási művelet nevét.

    1. A **Type (típus**) mezőbe írja be a **INSERT_HTTP_HEADER** értéket.

    1. A **fejléc neve** mezőbe írja be a fejléc nevét (ebben a példában a _SecretID_-t használjuk).

    1. A **kifejezés** mezőbe írja be az **AAA értéket. Felhasználói. ATTRIBÚTUM ("mySecretID")**, ahol a **MySecretID** a Citrix ADC-nek eljuttatott Azure ad SAML-jogcím.

    1. Válassza a **Létrehozás** lehetőséget.

    ![Citrix ADC konfiguráció – Újraírási művelet létrehozása ablaktábla](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Újraírási szabályzat létrehozása

1.  Ugrás a **AppExpert** újraírása  >    >  **Újraírási házirendjeire**.
 
    ![Citrix ADC konfiguráció-Újraírási házirendek panel](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Válassza a **Hozzáadás** lehetőséget, majd hajtsa végre a következő lépéseket:

    1. A **név** mezőben adja meg az Újraírási házirend nevét.

    1. A **művelet** mezőben válassza ki az előző szakaszban létrehozott Újraírási műveletet.

    1. A **kifejezés** mezőben adja meg az **igaz** értéket.

    1. Válassza a **Létrehozás** lehetőséget.

    ![Citrix ADC konfiguráció – Újraírási házirend létrehozása panel](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Újraírási házirend kötése egy virtuális kiszolgálóhoz

Újraírási házirend kötése egy virtuális kiszolgálóhoz a grafikus felhasználói felület használatával:

1. Nyissa meg a **Traffic Management**  >  **terheléselosztási**  >  **virtuális kiszolgálókat**.

1. A virtuális kiszolgálók listájában válassza ki azt a virtuális kiszolgálót, amelyhez az Újraírási házirendet kötni szeretné, majd válassza a **Megnyitás** lehetőséget.

1. A terheléselosztási **virtuális kiszolgáló** ablaktábla **Speciális beállítások** területén válassza a **házirendek** elemet. Az NetScaler-példányhoz konfigurált összes házirend megjelenik a listában.
 
    ![A "név", "művelet" és "kifejezés" mezőket és a "Create" (létrehozás) gombot megjelenítő képernyőkép.](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix ADC konfiguráció – virtuális kiszolgáló terheléselosztása panel](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Jelölje be a virtuális kiszolgálóhoz kötni kívánt szabályzat neve melletti jelölőnégyzetet.
 
    ![Citrix ADC konfiguráció – virtuális kiszolgáló forgalmi házirendjének kötési paneljének terheléselosztása](./media/header-citrix-netscaler-tutorial/header08.png)

1. A **típus kiválasztása** párbeszédpanelen:

    1. A **házirend kiválasztása** lapon válassza a **forgalom** lehetőséget.

    1. Válassza a **típus lehetőséget**, majd a **kérelem** lehetőséget.

    ![Citrix ADC-konfiguráció – házirendek párbeszédpanel](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Válassza az **OK** lehetőséget. Az állapotsorban lévő üzenet azt jelzi, hogy a házirend konfigurálása sikeres volt.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>Az SAML-kiszolgáló módosítása a jogcímek attribútumainak kinyeréséhez

1.  Válassza a **Biztonság**  >  **AAA-alkalmazás forgalmi**  >  **szabályzatok**  >  **hitelesítés**  >  **speciális házirendek**  >  **műveletek**  >  **kiszolgálók** lehetőséget.

1.  Válassza ki a megfelelő hitelesítési SAML-kiszolgálót az alkalmazáshoz.
 
    ![Citrix ADC konfiguráció – az SAML-kiszolgáló hitelesítésének konfigurálása panel](./media/header-citrix-netscaler-tutorial/header09.png)

1. Az **attribútumok** fájdalom mezőben adja meg a kiolvasni kívánt SAML-attribútumokat vesszővel elválasztva. A példánkban megadjuk az attribútumot `mySecretID` .
 
    ![Citrix ADC-konfiguráció – attribútumok ablaktábla](./media/header-citrix-netscaler-tutorial/header10.png)

1. A hozzáférés ellenőrzéséhez a böngésző URL-címében keresse meg az SAML attribútumot a **fejlécek gyűjtemény** alatt.

    ![Citrix ADC-konfiguráció – fejlécek gyűjtemény az URL-címen](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-adc-test-user"></a>Citrix ADC-teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Citrix ADC-ben. A Citrix ADC támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nem végezhető művelet. Ha egy felhasználó még nem létezik a Citrix ADC-ben, akkor a hitelesítés után létrejön egy újat.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [CITRIX ADC ügyfél-támogatási csapatához](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a Citrix ADC bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Citrix ADC bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Citrix ADC-csempére kattint, a rendszer átirányítja a Citrix ADC bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Citrix ADC konfigurálását követően kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).