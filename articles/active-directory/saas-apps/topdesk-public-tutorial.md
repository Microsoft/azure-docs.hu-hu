---
title: 'Oktatóanyag: Azure Active Directory topdesk integrációja – Nyilvános | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory topdesk – Public (Nyilvános) és TOPdesk (Nyilvános) között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 5d16fd87b01db69d3f55e22aad573b7847b9048c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518083"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Oktatóanyag: Azure Active Directory topdesk integrációja – Nyilvános

Ez az oktatóanyag bemutatja, hogyan integrálhatja a TOPdesk – Nyilvános szolgáltatásokat a Azure Active Directory (Azure AD) használatával. A TOPdesk – Nyilvános és az Azure AD integrálása után a következő funkciókat használhatja:

* Az Azure AD vezérlése, aki hozzáféréssel rendelkezik a TOPdesk – Nyilvános hozzáféréshez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a TOPdesk – Nyilvános szolgáltatásba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* TOPdesk – Nyilvános egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* TOPdesk – A nyilvános támogatja **az SP által kezdeményezett** SSO-t.

## <a name="add-topdesk---public-from-the-gallery"></a>TOPdesk – Nyilvános hozzáadása a katalógusból

A TOPdesk – Nyilvános az Azure AD-be integrálásának konfigurálásához hozzá kell adni a toPdesk – Public szolgáltatásokat a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a keresőmezőbe a **TOPdesk - Public (TOPdesk – Nyilvános)** parancsot.
1. Válassza **a TOPdesk – Nyilvános lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>Azure AD SSO konfigurálása és tesztelése TOPdeskhez – Nyilvános

Az Azure AD SSO konfigurálása és tesztelése TOPdesk használatával – Nyilvános egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a TOPdesk – Nyilvános helyen.

Az Azure AD SSO nyilvános TOPdesk használatával való konfiguráláshoz és teszteléshez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használják ezt a funkciót.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A TOPdesk – nyilvános SSO](#configure-topdesk---public-sso)** konfigurálása az egyszeri bejelentkezési beállítások alkalmazásoldali konfigurálásához.
    1. **[ToPdesk](#create-topdesk---public-test-user)** létrehozása – Nyilvános tesztfelhasználó – a B.Simon megfelelője a TOPdeskben – Nyilvános, amely a felhasználó Azure AD-ábrázoláshoz kapcsolódik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **TOPdesk – Nyilvános** alkalmazásintegráció lapon keresse meg a **Kezelés** szakaszt, és válassza az egyszeri **bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. A Set **up single sign-on with SAML** (Egyszeri bejelentkezés beállítása SAML-sel) oldalon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4.  Ha a Szolgáltató metaadatfájlja van, az **SAML-konfiguráció** alapszintű szakaszában hajtsa végre a következő lépéseket:

    >[!NOTE]
    >A szolgáltató **metaadatfájlját** a **CONFIGURE TOPdesk - Public Single Sign-On (TOPdesk** konfigurálása – nyilvános egyszeri bejelentkezés konfigurálása) című szakaszban fogja lehozni, amelyet az oktatóanyag későbbi részében ismertetünk.

    a. Kattintson **a Metaadatfájl feltöltése elemre.**
    
    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadatfájl kiválasztásához, majd kattintson a **Feltöltés elemre.**

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után az **Identifier** (Azonosító) és a Reply URL (Válasz **URL-cím)** értékek automatikusan ki vannak töltve az SamL-alapkonfiguráció szakaszban.

    d. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<companyname>.topdesk.net`

    e. Az Azonosító **URL-címe** szövegmezőben adja meg a TOPdesk-metaadatok URL-címét, amely a TOPdesk konfigurációból olvasható be. A következő mintát kell használnia: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi mintát használva: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Ha az **Azonosító és** a **Válasz URL-cím** értékeket a rendszer nem tölti ki automatikusan, manuálisan kell megadnia őket. Az Azonosító mezőben kövesse a fent említett mintát, és a VÁLASZ URL-cím értékét a **TOPdesk** konfigurálása – Nyilvános egyszeri bejelentkezés szakaszból fogja kapni, amelyet az oktatóanyag későbbi részében ismertetünk. A **Bejelentkezési URL-cím** értéke nem valós, ezért az értéket a tényleges URL-Sign-On kell frissítenie. Az értékért lépjen kapcsolatba a [TOPdesk – Nyilvános](https://help.topdesk.com/saas/enterprise/user/) ügyféltámogatási csapattal. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

5. A **Set up Single Sign-On with SAML** (Egyszeri bejelentkezés beállítása SAML-tanúsítványsal) lapon, az **SAML signing Certificate (SAML-aláíró** tanúsítvány) szakaszban kattintson a **Download** (Letöltés) elemre az összevonási metaadatok **XML-fájljának** letöltéséhez a megadott beállításokból igény szerint, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

6. A **TOPdesk** – Nyilvános beállítás szakaszban másolja ki a megfelelő URL-cím(eke)t a saját követelményének megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD-tesztfelhasználó létrehozása 

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza Azure Portal lehetőséget, **Azure Active Directory** **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezni fogja a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát. Ehhez hozzáférést biztosít a TOPdesk – Public szolgáltatáshoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **TOPdesk – Nyilvános lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az &quot;Alapértelmezett hozzáférés&quot; szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name=&quot;configure-topdesk---public-sso&quot;></a>A TOPdesk konfigurálása – Nyilvános SSO

1. Jelentkezzen be a **TOPdesk – Nyilvános** vállalati webhelyre rendszergazdaként.

2. A **TOPdesk menüben** kattintson a **Beállítások elemre.**
   
    ![Beállítások](./media/topdesk-public-tutorial/menu.png &quot;Beállítások")

3. Kattintson **a Bejelentkezési beállítások elemre.**
   
    ![Bejelentkezési beállítások](./media/topdesk-public-tutorial/login.png "Bejelentkezési beállítások")

4. Bontsa ki **a Bejelentkezési beállítások menüt,** majd kattintson az **Általános elemre.**
   
    ![Általános beállítások](./media/topdesk-public-tutorial/general.png "Általános beállítások")

5. Az **SAML-bejelentkezés** konfigurációja szakasz Nyilvános **szakaszában** hajtsa végre a következő lépéseket:
   
    ![Technikai beállítások](./media/topdesk-public-tutorial/public.png "Technikai beállítások")
   
    a. Kattintson **a Letöltés** gombra a nyilvános metaadatfájl letöltéséhez, majd mentse helyileg a számítógépre.
   
    b. Nyissa meg a letöltött metaadatfájlt, majd keresse meg az **AssertionConsumerService csomópontot.**

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. Másolja ki **az AssertionConsumerService** értéket, és illessze be ezt az értéket a Válasz **URL-cím** szövegmezőbe az **Alapszintű SAML-konfiguráció szakaszban.**      
   
6. Tanúsítványfájl létrehozásához hajtsa végre a következő lépéseket:
    
    ![Tanúsítvány](./media/topdesk-public-tutorial/certificate-file.png "Tanúsítvány")
    
    a. Nyissa meg a letöltött metaadatfájlt a Azure Portal.
    
    b. Bontsa ki azt a **RoleDescriptor** csomópontot, amely **xsi:type** **típusú fed:ApplicationServiceType.**
    
    c. Másolja ki az **X509Certificate csomópont** értékét.
    
    d. Mentse a másolt **X509Certificate** értéket helyileg a számítógépre egy fájlba.

7. A Nyilvános **szakaszban** kattintson a Hozzáadás **elemre.**
    
    ![SAML-bejelentkezés](./media/topdesk-public-tutorial/add.png "SAML-bejelentkezés")

8. Az **SAML konfigurációs segéd párbeszédpanelén** hajtsa végre a következő lépéseket:
    
    ![SAML konfigurációs segéd](./media/topdesk-public-tutorial/configuration.png "SAML Konfigurációs asszisztens")
    
    a. A letöltött metaadatfájl feltöltéséhez a Azure Portal **összevonási** metaadatok alatt kattintson a Tallózás **gombra.**

    b. A tanúsítványfájl feltöltése a Tanúsítvány **(RSA) alatt kattintson** a Tallózás **gombra.**

    c. A TOPdesk támogatási csapattól kapott emblémafájl feltöltéshez kattintson a Tallózás gombra az **Embléma ikon** **alatt.**

    d. A Felhasználónév **attribútuma** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    e. A **Megjelenítendő név** szövegmezőbe írja be a konfiguráció nevét.

    f. Kattintson a **Mentés** gombra.

### <a name="create-topdesk---public-test-user"></a>TOPdesk létrehozása – Nyilvános tesztfelhasználó

Ahhoz, hogy az Azure AD-felhasználók bejelentkeznek a TOPdesk – Nyilvános szolgáltatásba, ki kell őket kiépítenünk a TOPdesk – Nyilvános helyen. TOPdesk – Nyilvános esetén a kiépítés manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>A felhasználókiépítés konfiguráláshoz hajtsa végre a következő lépéseket:

1. Jelentkezzen be a **TOPdesk – Nyilvános vállalati** webhelyre rendszergazdaként.

2. A felső menüben kattintson a **TOPdesk New Support Files Person (TOPdesk \> Új támogatási fájlok \> \> személy) elemre.**
   
    ![Személy](./media/topdesk-public-tutorial/files.png "Személy")

3. Az Új személy párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Új személy](./media/topdesk-public-tutorial/new.png "Új személy")
   
    a. Kattintson az Általános lapra.

    b. A **Vezetéknév szövegmezőbe** írja be a felhasználó vezetéknevét (például Simon)
 
    c. Válasszon egy **webhelyet** a fiókhoz.
 
    d. Kattintson a **Mentés** gombra.

> [!NOTE]
> Bármely más TOPdesk – A TOPdesk által biztosított nyilvános felhasználói fiókok létrehozására használható eszközök vagy API-k – Nyilvános az Azure AD felhasználói fiókok létrehozásához.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a TOPdesk – Nyilvános bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a TOPdesk – Nyilvános bejelentkezési URL-címre, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Amikor a toPdesk – Nyilvános csempére kattint a Saját alkalmazások, a rendszer átirányítja a TOPdesk – Nyilvános bejelentkezési URL-címre. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A TOPdesk nyilvános konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
