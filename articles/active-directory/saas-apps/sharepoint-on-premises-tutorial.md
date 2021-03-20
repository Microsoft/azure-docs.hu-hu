---
title: 'Oktatóanyag: Azure Active Directory a helyszíni SharePoint-integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a helyszíni SharePoint között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: a693b22c609829f3bf6e76637eac5793d73703e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96862309"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses integráció a helyi SharePoint-környezettel

Ez az oktatóanyag azt ismerteti, hogyan integrálható a helyszíni SharePoint a Azure Active Directory (Azure AD) szolgáltatással. Ha a helyszíni SharePointot az Azure AD-vel integrálja, a következőket teheti:

* Szabályozhatja, hogy ki férhet hozzá a helyszíni SharePointhoz az Azure AD-ben.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a helyszíni SharePointba az Azure AD-fiókjával.
* A fiókokat a Azure Portal kezelheti.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció helyszíni SharePoint-környezettel való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy SharePoint 2013-Farm vagy újabb.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezést (SSO) konfigurálja és teszteli tesztkörnyezetben. Az Azure AD-felhasználók hozzáférhetnek a helyszíni SharePointhoz.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Vállalati alkalmazások létrehozása a Azure Portal

A helyi SharePoint-környezet Azure AD-be való integrálásának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a helyszíni SharePoint-szolgáltatást.

A SharePoint helyszíni hozzáadása a gyűjteményből:

1. A Azure Portal bal szélső paneljén válassza a **Azure Active Directory** lehetőséget.

   > [!NOTE]
   > Ha az elem nem érhető el, a bal oldali ablaktábla tetején található **minden szolgáltatás** hivatkozáson keresztül is megnyithatja. Az alábbi áttekintésben a **Azure Active Directory** hivatkozás az **identitás** szakaszban található. A szűrőt a szűrő mező használatával is megkeresheti.

1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

1. Új alkalmazás hozzáadásához válassza a párbeszédpanel tetején található **új alkalmazás** lehetőséget.

1. A keresőmezőbe írja be a **helyszíni SharePoint** kifejezést. Válassza a **helyszíni SharePoint** lehetőséget az eredmények ablaktáblán.

    <kbd>![Helyszíni SharePoint a találatok listájában](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Adja meg a SharePoint helyszíni példányának nevét, és válassza a **Hozzáadás** lehetőséget az alkalmazás hozzáadásához.

1. Az új vállalati alkalmazásban válassza a **Tulajdonságok** lehetőséget, és jelölje be a **szükséges értéket a felhasználói hozzárendeléshez?**.

   <kbd>![Felhasználói hozzárendelés szükséges? váltás](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   Ebben az esetben az érték a **nem** értékre van állítva.

## <a name="configure-and-test-azure-ad"></a>Az Azure AD konfigurálása és tesztelése

Ebben a szakaszban az Azure AD SSO-t konfigurálja a helyszíni SharePoint-környezettel. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a helyszíni SharePointban.

Az Azure AD SSO és a helyszíni SharePoint helyszíni konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

- [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
- [Konfigurálja a helyszíni SharePoint](#configure-sharepoint-on-premises) szolgáltatást az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
- [Hozzon létre egy Azure ad-tesztelési felhasználót a Azure Portal](#create-an-azure-ad-test-user-in-the-azure-portal) egy új felhasználó létrehozásához az Azure ad-ben az egyszeri bejelentkezéshez.
- [Hozzon létre egy Azure ad biztonsági csoportot a Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal) egy új biztonsági csoport létrehozásához az Azure ad-ben az egyszeri bejelentkezéshez.
- [Engedélyeket adhat egy Azure ad-fióknak a helyszíni SharePointban](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) az Azure ad-felhasználók engedélyeinek biztosításához.
- [Engedélyeket adhat egy Azure ad-csoportnak a helyszíni SharePointban](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) az Azure ad-csoport engedélyeinek biztosításához.
- [Hozzáférés biztosítása egy vendég fiókhoz a helyszíni sharepointhoz a Azure Portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) , hogy engedélyt adjon a vendég fióknak az Azure ad-ben a helyszíni sharepointhoz.
- [Konfigurálja úgy a megbízható identitás-szolgáltatót,](#configure-the-trusted-identity-provider-for-multiple-web-applications) hogy több webalkalmazás ugyanazt a megbízható identitás-szolgáltatót használja több webalkalmazáshoz.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD SSO-t a Azure Portalban.

Az Azure AD SSO konfigurálása a helyszíni SharePoint szolgáltatással:

1. A Azure Portal válassza a **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget. Válassza ki a korábban létrehozott vállalati alkalmazás nevét, és válassza az **egyszeri bejelentkezés** lehetőséget.

1. Az **egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza ki az **SAML** -módot az egyszeri bejelentkezés engedélyezéséhez.
 
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

1. Az **alapszintű SAML-konfiguráció** szakaszban kövesse az alábbi lépéseket:

    ![Helyszíni SharePoint-tartomány és URL-címek SSO-információi](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Az **azonosító** mezőben adja meg az URL-címet a következő minta használatával: `urn:<sharepointFarmName>:<federationName>` .

    1. A **Válasz URL-címe** mezőbe írja be az URL-címet a következő minta használatával: `https://<YourSharePointSiteURL>/_trust/` .

    1. A **bejelentkezési URL** -cím mezőbe írja be az URL-címet a következő minta használatával: `https://<YourSharePointSiteURL>/` .
    1. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel.

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. A helyszíni **SharePoint beállítása** szakaszban másolja a megfelelő URL-címeket a követelmény alapján:
    
    - **Bejelentkezési URL-cím**
    
        Másolja a bejelentkezési URL-címet, és cserélje le a **/saml2** a **/wsfed** végére, hogy az a következőképpen néz ki: https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed . (Ez az URL-cím nem pontos.)

    - **Azure AD-azonosító**
    - **Kijelentkezési URL-cím**

    > [!NOTE]
    > Ez az URL-cím nem használható a SharePointban. A **/saml2** a **/wsfed**-mel kell helyettesítenie. A helyszíni SharePoint-alkalmazás SAML 1,1-tokent használ, így az Azure AD egy WS fed-kérelmet vár a SharePoint-kiszolgálóról. A hitelesítés után az SAML 1,1 tokent bocsát ki.

### <a name="configure-sharepoint-on-premises"></a>A helyszíni SharePoint konfigurálása

1. Hozzon létre egy új megbízható identitás-szolgáltatót a SharePoint Server 2016-ben.

    Jelentkezzen be a SharePoint-kiszolgálóra, és nyissa meg a SharePoint felügyeleti rendszerhéjat. Adja meg az értékeket:
    - a **$Realm** a Azure Portal SharePoint helyszíni tartomány és URL-címek szakaszának azonosító értéke.
    - **$wsfedurl** az egyszeri bejelentkezési szolgáltatás URL-címe.
    - **$filepath** a fájl elérési útja, amelyhez a Azure Portalból letöltötte a tanúsítványfájl-fájlt.

    Futtassa az alábbi parancsokat egy új megbízható identitás-szolgáltató konfigurálásához.

    > [!TIP]
    > Ha most ismerkedik a PowerShell használatával, vagy szeretne többet megtudni a PowerShell működéséről, tekintse meg a [SharePoint PowerShellt](/powershell/sharepoint/overview).


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. Engedélyezze az alkalmazás megbízható identitás-szolgáltatóját.

    1. A **központi felügyelet** területen lépjen a **webalkalmazás kezelése lapra** , és válassza ki azt a webalkalmazást, amelyet az Azure ad-vel szeretne védeni.

    1. A menüszalagon válassza a **hitelesítésszolgáltatók** lehetőséget, majd válassza ki a használni kívánt zónát.

    1. Válassza a **megbízható identitás-szolgáltató** lehetőséget, majd válassza ki az imént regisztrált szolgáltatót a *AzureAD*.

    1. Válassza az **OK** lehetőséget.

    ![A hitelesítési szolgáltató konfigurálása](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Azure AD-teszt felhasználó létrehozása a Azure Portalban

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Azure Portalban.

1. A Azure Portal bal szélső paneljén válassza a **Azure Active Directory** lehetőséget. A **kezelés** ablaktáblán válassza a **felhasználók** lehetőséget.

1. Válassza a **minden felhasználó**  >  **új felhasználó** lehetőséget a képernyő tetején.

1. Válassza a **felhasználó létrehozása** lehetőséget, majd a felhasználó tulajdonságainál kövesse az alábbi lépéseket. Előfordulhat, hogy a bérlői utótag vagy bármely ellenőrzött tartomány használatával létrehozhat felhasználókat az Azure AD-ben. 

    1. A **név** mezőbe írja be a felhasználónevet. A **tesztfelhasználó** használtuk.
  
    1. A **Felhasználónév** mezőben adja meg a következőt: `TestUser@yourcompanydomain.extension`. Ez a példa a következőt mutatja: `TestUser@contoso.com` .

       ![A felhasználó párbeszédpanel](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

    1. Válassza a **Létrehozás** lehetőséget.

    1. Most már megoszthatja a webhelyet, TestUser@contoso.com és engedélyezheti, hogy ez a felhasználó hozzáférjen.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure AD biztonsági csoport létrehozása a Azure Portalban

1. Válassza ki **Azure Active Directory**  >  **csoportokat**.

1. Válassza az **új csoport** lehetőséget.

1. Adja meg a **csoport típusát**, a **csoport nevét**, a **csoport leírását** és a **tagság típusa** mezőket. Kattintson a nyilakra a tagok kiválasztásához, majd keresse meg vagy válassza ki a csoportba felvenni kívánt tagokat. A kijelölt tagok hozzáadásához válassza a **kijelölés** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

![Azure AD-biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>Engedélyek megadása egy Azure AD-fiók számára a helyszíni SharePointban

Ha hozzáférést szeretne biztosítani egy Azure AD-felhasználóhoz a helyszíni SharePointban, ossza meg a webhelycsoportot, vagy adja hozzá az Azure AD-felhasználót a webhelycsoport egyik csoportjához. A felhasználók mostantól bejelentkezhetnek a SharePoint-201x az Azure AD-ból származó identitások használatával, de a felhasználói élmény fokozása továbbra is lehetőség van. A felhasználók keresése például több keresési eredményt is tartalmaz a People választóban. A jogcím-hozzárendelésben létrehozott összes jogcím-típushoz keresési eredmények tartoznak. Ha a People Picker használatával szeretne felhasználót kiválasztani, pontosan meg kell adnia a felhasználónevét, és **a jogcím** eredményét kell választania.

![Jogcímek keresési eredményei](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

A keresett értékeket nem érvényesíti a rendszer, ami hibákat okozhat, vagy a felhasználók véletlenül helytelen jogcím-típust választanak. Ez a helyzet megakadályozza, hogy a felhasználók sikeresen hozzáférjenek az erőforrásokhoz.

Ha ezt a forgatókönyvet a People Picker használatával szeretné kijavítani, a [AzureCP](https://yvand.github.io/AzureCP/) nevű nyílt forráskódú megoldás egyéni jogcím-szolgáltatót biztosít a SharePoint 2013, 2016 és 2019 rendszerhez. A Microsoft Graph API-val oldja fel, hogy a felhasználók hogyan adja meg és hajtják végre az érvényesítést. További információ: [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > AzureCP nélkül hozzáadhat csoportokat az Azure AD-csoport AZONOSÍTÓjának hozzáadásával, de ez a módszer nem felhasználóbarát és megbízható. A következőképpen néz ki:
  > 
  >![Azure AD-csoport hozzáadása egy SharePoint-csoporthoz azonosító alapján](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>Engedélyek megadása egy Azure AD-csoport számára a helyszíni SharePointban

Ahhoz, hogy Azure AD-alapú biztonsági csoportokat rendeljen a SharePointhoz a helyszínen, a SharePoint Serverhez egy egyéni jogcím-szolgáltatót kell használnia. Ez a példa az AzureCP-t használja.

 > [!NOTE]
 > A AzureCP nem Microsoft-termék, és Microsoft ügyfélszolgálata nem támogatja. A helyszíni SharePoint-farm AzureCP letöltéséhez, telepítéséhez és konfigurálásához tekintse meg a [AzureCP](https://yvand.github.io/AzureCP/) webhelyét. 

1. Konfigurálja a AzureCP a helyszíni SharePoint-farmon vagy egy alternatív egyéni jogcím-szolgáltatói megoldáson. A AzureCP konfigurálásához tekintse meg ezt a [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html) webhelyét.

1. A Azure Portal válassza a **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget. Válassza ki a korábban létrehozott vállalati alkalmazás nevét, és válassza az **egyszeri bejelentkezés** lehetőséget.

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon szerkessze a **felhasználói attribútumok & jogcímek** szakaszt.

1. Válassza **a csoport hozzáadása** lehetőséget.

1. Válassza ki a felhasználóhoz társított csoportokat a jogcímben. Ebben az esetben válassza a **minden csoport** elemet. A **forrás attribútum** szakaszban válassza a **csoport azonosítója** elemet, majd válassza a **Mentés** lehetőséget.

Ha hozzáférést szeretne biztosítani az Azure AD biztonsági csoporthoz a helyszíni SharePointban, ossza meg a webhelycsoportot, vagy adja hozzá az Azure AD biztonsági csoportot a webhelycsoport egyik csoportjához.

1. Tallózással keresse meg a **SharePoint-webhelycsoportot**. A hely gyűjteményének **hely beállításai** területén válassza a **személyek és csoportok** lehetőséget. 

1. Válassza ki a SharePoint-csoportot, majd válassza az **új**  >  **felhasználók hozzáadása ehhez a csoporthoz** lehetőséget. A csoport nevének beírásakor a személyek választó megjeleníti az Azure AD biztonsági csoportot.

    ![Azure AD-csoport hozzáadása egy SharePoint-csoporthoz](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Hozzáférés biztosítása egy vendég fiókhoz a helyszíni SharePoint számára a Azure Portal

A SharePoint-webhelyét konzisztens módon is megadhatja a vendég fiókhoz, mivel az egyszerű felhasználónév már módosítva lesz. A felhasználó például a következőt `jdoe@outlook.com` jeleníti meg: `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` . A hely külső felhasználókkal való megosztásához hozzá kell adnia néhány módosítást a Azure Portal a **felhasználói attribútumok & jogcímek** szakaszban.

1. A Azure Portal válassza a **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget. Válassza ki a korábban létrehozott vállalati alkalmazás nevét, és válassza az **egyszeri bejelentkezés** lehetőséget.

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon szerkessze a **felhasználói attribútumok & jogcímek** szakaszt.

1. A **szükséges jogcím** zónában válassza az **egyedi felhasználói azonosító (név azonosítója)** elemet.

1. Módosítsa a **forrás attribútum** tulajdonságot a **User. localuserprincipalname** értékre, majd válassza a **Mentés** lehetőséget.

    ![Felhasználói attribútumok & jogcímek kezdeti forrás attribútuma](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. A menüszalag használatával térjen vissza az **SAML-alapú bejelentkezéshez**. Most a **felhasználói attribútumok & a jogcímek** szakasz a következőképpen néz ki: 

    ![Felhasználói attribútumok & jogcímek végleges](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > Ebben a beállításban nem szükséges a vezetékneve és a megadott név.

1. A Azure Portal bal szélső paneljén válassza a **Azure Active Directory** , majd a **felhasználók** lehetőséget.

1. Válassza az **új vendég felhasználó** lehetőséget.

1. Válassza a **felhasználó meghívása** lehetőséget. Adja meg a felhasználó tulajdonságait, és válassza a **meghívás** lehetőséget.

1. Most már megoszthatja a webhelyet, MyGuestAccount@outlook.com és engedélyezheti, hogy ez a felhasználó hozzáférjen.

    ![Hely megosztása vendég fiókkal](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>A megbízható identitás szolgáltatójának konfigurálása több webalkalmazáshoz

A konfiguráció egyetlen webalkalmazás esetében működik, de további konfigurálásra van szükség, ha ugyanazt a megbízható identitás-szolgáltatót szeretné használni több webalkalmazáshoz. Tegyük fel például, hogy kibővített egy webalkalmazást az URL-cím használatára, `https://sales.contoso.com` és most hitelesíteni szeretné a felhasználókat a szolgáltatásban `https://marketing.contoso.com` . Ehhez frissítse az WReply paramétert, és frissítse az alkalmazás regisztrációját az Azure AD-ben a válasz URL-címének hozzáadásához.

1. A Azure Portal válassza a **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget. Válassza ki a korábban létrehozott vállalati alkalmazás nevét, és válassza az **egyszeri bejelentkezés** lehetőséget.

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon szerkessze az **alapszintű SAML-konfigurációt**.

    ![Alapszintű SAML-konfiguráció](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. A **Válasz URL-címére (a fogyasztói szolgáltatás URL-címére)** adja hozzá a további webalkalmazások URL-címét, majd válassza a **Mentés** lehetőséget.

    ![Az alapszintű SAML-konfiguráció szerkesztése](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. A SharePoint-kiszolgálón nyissa meg a SharePoint 201x felügyeleti rendszerhéját, és futtassa a következő parancsokat. Használja a korábban használt megbízható azonosító jogkivonat-kiállító nevét.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. A **központi felügyelet** lapon nyissa meg a webalkalmazást, és engedélyezze a meglévő megbízható identitás-szolgáltatót.

Előfordulhat, hogy más forgatókönyvek is vannak, amelyekben a belső felhasználók számára hozzáférést kíván biztosítani a SharePoint helyszíni példányához. Ebben az esetben telepítenie kell Microsoft Azure Active Directory Connect a helyszíni felhasználók Azure AD-vel való szinkronizálásának engedélyezéséhez. Ezt a telepítést egy másik cikkben tárgyaljuk.

## <a name="next-steps"></a>Következő lépések

A helyszíni SharePoint konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
