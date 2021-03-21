---
title: 'Oktatóanyag: Azure Active Directory integráció a Qlik Sense Enterprise szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Qlik Sense Enterprise között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 2d046f5f039555e58d9ce4c028e750ce083fd5f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733689"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Oktatóanyag: a Qlik Sense Enterprise integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Qlik Sense Enterpriset Azure Active Directory (Azure AD) használatával. Ha integrálja az Qlik Sense Enterprise-t az Azure AD-vel, a következőket teheti:

* A Qlik Sense Enterprise-hoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Qlik Sense Enterprise-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Qlik Sense Enterprise egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. 
* A Qlik Sense Enterprise az **SP** által kezdeményezett egyszeri bejelentkezést támogatja.
* A Qlik Sense Enterprise **az** igény szerinti üzembe helyezést is támogatja

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Qlik Sense Enterprise hozzáadása a katalógusból

A Qlik Sense Enterprise az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Qlik Sense Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Qlik Sense Enterprise** kifejezést a keresőmezőbe.
1. Válassza ki a **Qlik Sense Enterprise** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-qlik-sense-enterprise"></a>Azure AD SSO konfigurálása és tesztelése a Qlik Sense Enterprise-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Qlik Sense Enterprise használatával egy **Britta Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Qlik Sense Enterprise-ban.

Az Azure AD SSO és a Qlik Sense Enterprise konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
1. **[Konfigurálja a Qlik Sense Enterprise SSO](#configure-qlik-sense-enterprise-sso)** -t – az egyes Sign-On beállítások konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre Qlik Sense Enterprise test User](#create-qlik-sense-enterprise-test-user)** – hogy a Britta Simon partnere legyen a Qlik Sense Enterprise-ban, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Qlik Sense Enterprise** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta egyikének használatával:

    | Azonosító |
    |-------------|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com` |
    | `https://<Fully Qualified Domain Name>.qliksense.com` |
    |
   

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel, amelyet az oktatóanyag későbbi részében ismertetünk, vagy forduljon a [Qlik Sense Enterprise ügyfél-támogatási csapatához](https://www.qlik.com/us/services/support) az értékek beszerzéséhez. Az URL-címek alapértelmezett portja 443, de a szervezet igényeinek megfelelően testre szabhatja.

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-** fájlját a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Qlik Sense Enterprise-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Qlik Sense Enterprise** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-qlik-sense-enterprise-sso"></a>A Qlik Sense Enterprise SSO konfigurálása

1. Készítse elő az összevonási metaadatok XML-fájlját, hogy feltölthető legyen a Qlik Sense Serverre.

    > [!NOTE]
    > Mielőtt feltölti a identitásszolgáltató-metaadatokat a Qlik Sense-kiszolgálóra, a fájlt módosítani kell, hogy eltávolítsa az adatokat az Azure AD és a Qlik Sense Server megfelelő működésének biztosítása érdekében.

    ![Képernyőfelvétel: egy Visual Studio Code-ablak, amely az összevonási metaadatok X M-fájlját tartalmazza.][qs24]

    a. Nyissa meg a FederationMetaData.xml fájlt, amelyet Azure Portal egy szövegszerkesztőben töltött le.

    b. Keresse meg a **securitytokenservicetype** értéket.  Négy bejegyzés létezik (két pár nyitó és záró elem címkéje).

    c. Törölje a Securitytokenservicetype címkéit és az összes adatot a fájlból.

    d. Mentse a fájlt, és tartsa a közelben a dokumentum későbbi részében való használatra.

2. Navigáljon a Qlik Sense Qlik felügyeleti konzolhoz (QMC) olyan felhasználóként, aki virtuális proxy-konfigurációkat hozhat létre.

3. A QMC kattintson a **virtuális proxyk** menüpontra.

    ![A képernyőfelvételen a rendszer konfigurálása elemből kiválasztott virtuális proxyk láthatók.][qs6]

4. A képernyő alján kattintson az **új létrehozása** gombra.

    ![A képernyőképen az új létrehozása lehetőség látható.][qs7]

5. Megjelenik a virtuális proxy szerkesztési képernyője.  A képernyő jobb oldalán egy menü jelenik meg a konfigurációs beállítások láthatóvá tételéhez.

    ![A képernyőfelvétel a tulajdonságok közül kiválasztott azonosítást jeleníti meg.][qs9]

6. Az azonosító menü beállítás bejelölésével adja meg az Azure virtuális proxy konfigurációjának azonosító adatait.

    ![Képernyőfelvétel: a virtuális proxy azonosításának szerkesztése szakasz, ahol megadhatja a leírt értékeket.][qs8]  

    a. A **Leírás** mező a virtuális proxy konfigurációjának rövid neve.  Adja meg a Leírás értékét.

    b. Az **előtag** mező azonosítja a virtuális proxy végpontját, amely az Azure ad egyszeri bejelentkezéssel való csatlakozáshoz kapcsolódik a Qlik-hez.  Adjon meg egy egyedi előtag-nevet ehhez a virtuális proxyhoz.

    c. A **munkamenet-inaktivitás időkorlátja (perc)** a virtuális proxyn keresztüli kapcsolatok időtúllépése.

    d. A **munkamenet-cookie fejlécének neve** az a cookie neve, amely a munkamenet-azonosítót tárolja a felhasználó által a sikeres hitelesítés után kapott Qlik.  A névnek egyedinek kell lennie.

7. Kattintson a hitelesítési menü lehetőségre, hogy láthatóvá tegye azt.  Megjelenik a hitelesítés képernyő.

    ![Képernyőfelvétel: a virtuális proxy hitelesítésének szerkesztése szakasz, ahol megadhatja a leírt értékeket.][qs10]

    a. A **Névtelen hozzáférési mód** legördülő lista meghatározza, hogy a névtelen felhasználók hozzáférhetnek-e a Qlik a virtuális proxyn keresztül.  Az alapértelmezett beállítás nem névtelen felhasználó.

    b. A **hitelesítési módszer** legördülő lista meghatározza azt a hitelesítési sémát, amelyet a virtuális proxy használni fog.  Válassza ki az SAML elemet a legördülő listából.  Ennek eredményeképpen további lehetőségek jelennek meg.

    c. Az **SAML-gazdagép URI-ja mezőben** adja meg, hogy az állomásnév felhasználói a Qlik értelmet az adott SAML virtuális proxyn keresztül férhessenek hozzá.  A hostname a Qlik Sense-kiszolgáló URI-ja.

    d. Az **SAML-entitás azonosítója** mezőben adja meg a SAML-gazdagép URI-mezőjében megadott értéket.

    e. Az **SAML-identitásszolgáltató metaadatai** az **összevonási metaadatok szerkesztése az Azure ad-konfiguráció** szakaszban korábban szerkesztett fájl.  **A identitásszolgáltató-metaadatok feltöltése előtt módosítani kell a fájlt** , hogy el lehessen távolítani az adatokat az Azure ad és a Qlik Sense Server közötti megfelelő működés biztosítása érdekében.  **Ha a fájlt még szerkeszteni szeretné, tekintse meg a fenti utasításokat.**  Ha a fájlt szerkesztették, kattintson a Tallózás gombra, és válassza ki a szerkesztett metaadat-fájlt, hogy feltöltse a virtuális proxy konfigurációba.

    f. Adja meg a **userid** Azure ad által az Qlik Sense-KISZOLGÁLÓNAK küldött SAML-attribútumhoz tartozó attribútum nevét vagy sémájának hivatkozását.  A séma-hivatkozási információk az Azure app screens szolgáltatásban a konfiguráció után érhetők el.  A Name attribútum használatához írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    : Adja meg a **felhasználói könyvtár** értékét, amely a felhasználókhoz lesz csatolva, amikor az Azure ad-n keresztül hitelesítik magukat a Qlik Sense Serveren.  A hardcoded értékeket **szögletes zárójelek ([]**) szerint kell megadni.  Az Azure AD SAML-kijelentésben eljuttatott attribútumok használatához a szövegmezőben szögletes zárójelek **nélkül** adja meg az attribútum nevét.

    h. Az **SAML-aláírási algoritmus** beállítja a szolgáltató (ebben az esetben a Qlik Sense Server) tanúsítványának aláírását a virtuális proxy konfigurációjában.  Ha a Qlik Sense Server a Microsoft Enhanced RSA és AES titkosítási szolgáltató használatával generált megbízható tanúsítványt használ, módosítsa az SAML-aláírási algoritmust az **SHA-256** értékre.

    i. A SAML-attribútumok leképezése szakasz további attribútumokat is lehetővé tesz, például a csoportok számára a biztonsági szabályokban való használatra Qlik.

8. Kattintson a **terheléselosztás** menüpontra, hogy láthatóvá tegye azt.  Megjelenik a terheléselosztás képernyője.

    ![A képernyőfelvételen a virtuális proxy szerkesztési képernyője látható a TERHELÉSELOSZTÁShoz, ahol kiválaszthatja az új kiszolgáló hozzáadása csomópontot.][qs11]

9. Kattintson az **új kiszolgáló csomópont hozzáadása** gombra, válassza a motor csomópont vagy a csomópontok Qlik a munkamenetek küldése terheléselosztás céljából lehetőséget, majd kattintson a **Hozzáadás** gombra.

    ![Képernyőfelvétel: a kiszolgáló-csomópontok hozzáadása az egyenleg betöltéséhez párbeszédpanel, ahol kiszolgálókat adhat hozzá.][qs12]

10. A Speciális menü lehetőségre kattintva láthatóvá teheti azt. Megjelenik a speciális képernyő.

    ![A képernyőképen a virtuális proxy szerkesztése speciális képernyő látható.][qs13]

    A gazdagép engedélyezési listája azonosítja azokat a gazdagépeket, amelyeket a rendszer az Qlik Sense-kiszolgálóhoz való csatlakozáskor elfogad.  **Adja meg a hostname Users (állomásnév) felhasználók által megadott értéket a Qlik Sense Serverhez való csatlakozáskor.** Az állomásnév ugyanaz az érték, mint az SAML-gazdagép URI-ja a https://nélkül.

11. Kattintson az **alkalmaz** gombra.

    ![A képernyőképen az Apply (alkalmaz) gomb látható.][qs14]

12. Kattintson az OK gombra azon figyelmeztető üzenet elfogadásához, amelyet a virtuális proxyhoz kapcsolódó állapotú proxyk újraindítása fog.

    ![A képernyőfelvételen a virtuális proxy megerősítő üzenet módosításainak alkalmazása látható.][qs15]

13. A képernyő jobb oldalán megjelenik a társított elemek menü.  Kattintson a **proxyk** menüpontra.

    ![A képernyőképen a társított elemek közül kiválasztott proxyk láthatók.][qs16]

14. Megjelenik a proxy képernyő.  Kattintson a lenti **hivatkozás** gombra a proxy virtuális proxyhoz való csatolásához.

    ![Képernyőfelvétel: a hivatkozás gomb.][qs17]

15. Válassza ki azt a proxy csomópontot, amely támogatni fogja ezt a virtuális proxy-kapcsolatot, majd kattintson a **hivatkozás** gombra.  A csatolás után a proxy a társított proxyk területen jelenik meg.

    ![Képernyőfelvétel: Select proxy Services.][qs18]
  
    ![Képernyőfelvétel: a társított proxyk megjelenítése a virtuális proxyhoz társított elemek párbeszédpanelen.][qs19]

16. Körülbelül öt – tíz másodperc elteltével megjelenik a frissítési QMC üzenet.  Kattintson a **QMC frissítése** gombra.

    ![Képernyőfelvétel: az az üzenet, amelyen a munkamenet véget ért.][qs20]

17. A QMC frissítésekor kattintson a **virtuális proxyk** menüpontra. Az új SAML virtuális proxy bejegyzés megjelenik a képernyőn látható táblázatban.  Egyetlen kattintással a virtuális proxy bejegyzésére.

    ![A képernyőfelvételen egyetlen bejegyzéssel rendelkező virtuális proxyk láthatók.][qs51]

18. A képernyő alján az SP-metaadatok letöltése gomb aktiválva lesz.  A metaadatok fájlba mentéséhez kattintson az **SP-metaadatok letöltése** gombra.

    ![A képernyőképen a letöltés S P metaadatokat ábrázoló gomb látható.][qs52]

19. Nyissa meg az SP metaadat-fájlt.  Figyelje meg a **entityID** bejegyzést és a **AssertionConsumerService** bejegyzést.  Ezek az értékek egyenértékűek az **azonosítóval**, a **bejelentkezési URL** -címmel és a **Válasz URL-címével** az Azure ad-alkalmazás konfigurációjában. Illessze be ezeket az értékeket a **Qlik Sense Enterprise domain és URLs** szakaszba az Azure ad-alkalmazás konfigurációjában, ha nem megfelelőek, majd cserélje le őket a Azure ad alkalmazás konfigurációs varázslóban.

    ![A képernyőfelvétel egy egyszerű szövegszerkesztőt mutat be a entityID és a AssertionConsumerService nevű EntityDescriptor.][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Qlik Sense Enterprise test User létrehozása

A Qlik Sense Enterprise az igény szerinti **üzembe** helyezést is támogatja, a felhasználók automatikusan hozzáadódnak a Qlik Sense Enterprise felhasználói adattárához, mivel az egyszeri bejelentkezés funkciót használják. Emellett az ügyfelek használhatják az QMC-t, és létrehozhatnak egy UDC-t (User Directory-összekötőt) az Qlik Sense Enterprise-ban lévő felhasználók előzetes kitöltéséhez, például a Active Directory és mások számára.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a Qlik Sense Enterprise bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a Qlik Sense Enterprise bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Qlik Sense Enterprise csempére kattint, a rendszer átirányítja a Qlik Sense Enterprise bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a Qlik Sense Enterprise-t, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png