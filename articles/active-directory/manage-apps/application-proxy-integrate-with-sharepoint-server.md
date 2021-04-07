---
title: Távoli hozzáférés engedélyezése a SharePointhoz – Azure AD Application Proxy
description: Ismerteti a helyszíni SharePoint Server és az Azure AD Application Proxy integrálásának alapjait.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b5895d016e2d9d9b471218bc083ea7585254b45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258681"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Távoli hozzáférés engedélyezése a SharePointhoz az Azure AD-alkalmazásproxy segítségével

Ez a részletes útmutató azt ismerteti, hogyan integrálható egy helyszíni SharePoint-farm Azure Active Directory (Azure AD) alkalmazásproxy használatával.

## <a name="prerequisites"></a>Előfeltételek

A konfiguráció végrehajtásához a következő erőforrásokra van szükség:
- Egy SharePoint 2013-Farm vagy újabb.
- Egy Azure AD-bérlő az alkalmazásproxy részét képező csomaggal. További információ az [Azure ad-csomagokról és a díjszabásról](https://azure.microsoft.com/pricing/details/active-directory/).
- Egy [Egyéni, ellenőrzött tartomány](../fundamentals/add-custom-domain.md) az Azure ad-bérlőben.
- A helyszíni Active Directory szinkronizálva Azure AD Connectval, amelyen keresztül a felhasználók [bejelentkezhetnek az Azure](../hybrid/plan-connect-user-signin.md)-ba.
- Egy, a vállalati tartományon belüli gépen telepített és futó alkalmazásproxy-összekötő.

A SharePoint és az alkalmazásproxy konfigurálásához két URL-cím szükséges:
- Egy külső URL-cím, amely a végfelhasználók számára látható, és az Azure AD-ben van meghatározva. Ez az URL-cím egyéni tartományt is használhat. További információ az [Egyéni tartományok Azure ad Application proxy-beli használatáról](application-proxy-configure-custom-domain.md).
- Belső URL-cím, amely csak a vállalati tartományon belül ismert, és soha nem használható közvetlenül.

> [!IMPORTANT]
> Ahhoz, hogy a hivatkozások megfelelően legyenek leképezve, a belső URL-címhez kövesse az alábbi javaslatokat:
> - HTTPS használata.
> - Ne használjon egyéni portokat.
> - A vállalati tartománynévrendszer (DNS) területen hozzon létre egy gazdagépet (A), hogy a SharePoint-WFE (vagy terheléselosztó) mutasson, és ne egy aliast (CName).

Ez a cikk a következő értékeket használja:
- Belső URL-cím: `https://sharepoint`
- Külső URL-cím: `https://spsites-demo1984.msappproxy.net/`
- Alkalmazáskészlet-fiók a SharePoint-webalkalmazáshoz: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>1. lépés: alkalmazás konfigurálása az Azure AD-ben, amely alkalmazásproxy-t használ

Ebben a lépésben létrehoz egy alkalmazást az Azure Active Directory-bérlőben, amely alkalmazásproxy-t használ. A külső URL-címet kell megadnia, és meg kell adnia a belső URL-címet, mindkettőt a SharePointban később használják.

1. Hozza létre az alkalmazást a következő beállításokkal leírtak szerint. Részletes útmutatásért lásd: [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Belső URL-cím**: a SharePoint belső URL-címe, amelyet később fog beállítani a SharePointban, például `https://sharepoint` .
   * **Előzetes hitelesítés**: Azure Active Directory
   * **URL-címek lefordítása a fejlécekben**: nem
   * **URL-címek fordítása az alkalmazás törzsében**: nem

   ![SharePoint alkalmazásként való közzététele](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Az alkalmazás közzététele után kövesse az alábbi lépéseket az egyszeri bejelentkezés beállításainak konfigurálásához:

   1. A portálon az alkalmazás lapján válassza az **Egyszeri bejelentkezés** lehetőséget.
   1. Az **Egyszeri bejelentkezési mód** esetében válassza az **Integrált Windows-hitelesítés** lehetőséget.
   1. A **belső alkalmazás SPN** beállítása a korábban megadott értékre. Ebben a példában az érték a következő: `HTTP/sharepoint` .
   1. A **delegált bejelentkezési azonosító** területen válassza ki a Active Directory erdő konfigurációjának legmegfelelőbb lehetőségét. Ha például egyetlen Active Directory tartománya van az erdőben, válassza **a helyszíni Sam-fiók neve** lehetőséget (az alábbi képernyőképen látható módon). Ha azonban a felhasználók nem ugyanabban a tartományban vannak, mint a SharePoint és az alkalmazásproxy-összekötő kiszolgálók, válassza **a helyszíni egyszerű felhasználónév** lehetőséget (a képernyőképen nem látható).

   ![Integrált Windows-hitelesítés konfigurálása egyszeri bejelentkezéshez](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Az alkalmazás beállításának befejezéséhez lépjen a **Felhasználók és csoportok** szakaszra, és rendelje hozzá a felhasználókat, akik hozzáférhetnek az alkalmazáshoz. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>2. lépés: a SharePoint-webalkalmazás konfigurálása

A SharePoint-webalkalmazást Kerberos-kapcsolattal kell konfigurálni, valamint a megfelelő alternatív hozzáférés-leképezéseket, hogy megfelelően működjenek az Azure AD Application Proxy. Két lehetőség közül választhat:

- Hozzon létre egy új webalkalmazást, és csak az alapértelmezett zónát használja. Ez az előnyben részesített lehetőség, mivel a SharePoint szolgáltatással kapcsolatos legjobb élményt nyújtja (például a SharePoint által létrehozott e-mail-riasztások hivatkozásai mindig az alapértelmezett zónára mutatnak).
- Meglévő webalkalmazás kiterjesztése a Kerberos konfigurálására nem alapértelmezett zónában.

> [!IMPORTANT]
> A használt zónától függetlenül a SharePoint-webalkalmazás alkalmazáskészlet-fiókjának egy tartományi fióknak kell lennie a Kerberos megfelelő működéséhez.

### <a name="provision-the-sharepoint-web-application"></a>A SharePoint-webalkalmazás kiépítése

- Ha új webalkalmazást hoz létre, és csak az alapértelmezett zónát használja (előnyben részesített beállítás):

    1. Indítsa el a **SharePoint felügyeleti rendszerhéjat** , és futtassa a következő parancsfájlt:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Nyissa meg a **SharePoint központi felügyeleti** webhelyet.
    1. A **Rendszerbeállítások** területen válassza a **másodlagos hozzáférés-leképezések konfigurálása** elemet. Megnyílik a **másodlagos hozzáférés-leképezési gyűjtemény** ablak.
    1. Szűrje a kijelzőt az új webalkalmazással, és erősítse meg, hogy a következőhöz hasonló jelenik meg:

       ![Webalkalmazás másodlagos hozzáférés-leképezései](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Ha egy meglévő webalkalmazást új zónára terjeszt (ha nem tudja használni az alapértelmezett zónát):

    1. Indítsa el a SharePoint felügyeleti rendszerhéjat, és futtassa a következő parancsfájlt:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Nyissa meg a **SharePoint központi felügyeleti** webhelyet.
    1. A **Rendszerbeállítások** területen válassza a **másodlagos hozzáférés-leképezések konfigurálása** elemet. Megnyílik a **másodlagos hozzáférés-leképezési gyűjtemény** ablak.
    1. Szűrje a kijelzőt a kibővített webalkalmazással, és erősítse meg, hogy a következőhöz hasonló jelenik meg:

        ![Kiterjesztett alkalmazás másodlagos hozzáférés-leképezései](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Győződjön meg arról, hogy a SharePoint-webalkalmazás egy tartományi fiók alatt fut

A SharePoint-webalkalmazás alkalmazáskészletét futtató fiók azonosításához és a tartományi fiók biztosításához kövesse az alábbi lépéseket:

1. Nyissa meg a **SharePoint központi felügyeleti** webhelyet.
1. Lépjen a **Biztonság** elemre, és válassza a **szolgáltatásfiókok konfigurálása** lehetőséget.
1. Válassza a **webalkalmazás-készlet – YourWebApplicationName** elemet.

   ![A szolgáltatásfiók konfigurálásának lehetőségei](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Győződjön meg róla, hogy az **összetevőhöz tartozó fiók kiválasztása** tartományi fiókot ad vissza, és jegyezze fel, mert a következő lépésben szükség lesz rá.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Győződjön meg arról, hogy az extranetes zóna IIS-helyéhez konfigurálva van egy HTTPS-tanúsítvány

Mivel a belső URL-cím HTTPS protokollt () használ, meg kell `https://SharePoint/` adni egy tanúsítványt a Internet Information Services (IIS) helyen.

1. Nyissa meg a Windows PowerShell-konzolt.
1. Futtassa az alábbi szkriptet egy önaláírt tanúsítvány létrehozásához, és adja hozzá a számítógép saját áruházához:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Az önaláírt tanúsítványok csak tesztelési célokra használhatók. Éles környezetekben erősen ajánlott a hitelesítésszolgáltató által kiadott tanúsítványok használata.

1. Nyissa meg a Internet Information Services Manager konzolt.
1. Bontsa ki a kiszolgálót a fanézetben, bontsa ki a **helyek** csomópontot, válassza ki a **SharePoint-HRE proxy** helyet, és válassza a **kötések** lehetőséget.
1. Válassza a **https-kötés** lehetőséget, majd válassza a **Szerkesztés** lehetőséget.
1. A TLS/SSL-tanúsítvány mezőben válassza a **SharePoint** -tanúsítvány lehetőséget, majd kattintson **az OK gombra**.

Mostantól az Azure AD Application Proxyon kívül is elérheti a SharePoint-webhelyet.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>3. lépés: a Kerberos által korlátozott delegálás konfigurálása

A felhasználók először az Azure AD-ben, majd a SharePointban az Azure AD proxy-összekötőn keresztül végzik el a hitelesítést. Ahhoz, hogy az összekötő megszerezzen egy Kerberos-tokent az Azure AD-felhasználó nevében, konfigurálnia kell a Kerberos által korlátozott delegálást (KCD) a protokoll-átmenettel. További információ a KCD: a [Kerberos által korlátozott delegálás áttekintése](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>A SharePoint-szolgáltatásfiók SPN-értékének beállítása

Ebben a cikkben a belső URL-cím `https://sharepoint` , így az egyszerű szolgáltatásnév (SPN) `HTTP/sharepoint` . Ezeket az értékeket a környezetének megfelelő értékekkel kell helyettesítenie.
Ha a SharePoint-alkalmazáskészlet fiókjának SPN-regisztrációját szeretné regisztrálni `HTTP/sharepoint` `Contoso\spapppool` , futtassa a következő parancsot egy parancssorból a tartomány rendszergazdájaként:

`setspn -S HTTP/sharepoint Contoso\spapppool`

A `Setspn` parancs a Hozzáadás előtt megkeresi az egyszerű szolgáltatásnevet. Ha az egyszerű szolgáltatásnév már létezik, **duplikált SPN-érték** jelenik meg. Ebben az esetben érdemes eltávolítani a meglévő SPN-t, ha az nem a megfelelő alkalmazáskészlet-fiókban van beállítva. A `Setspn` parancs az-L kapcsolóval való futtatásával ellenőrizheti, hogy az egyszerű szolgáltatásnév hozzáadása sikeres volt-e. További információ a parancsról: [Setspn](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Győződjön meg arról, hogy az összekötő megbízható a SharePoint-alkalmazáskészlethez hozzáadott egyszerű szolgáltatásnév delegálásához

Konfigurálja úgy a KCD, hogy az Azure AD Application Proxy szolgáltatás delegálja a felhasználói identitásokat a SharePoint-alkalmazáskészlet fiókjának. Konfigurálja a KCD-t úgy, hogy engedélyezze az alkalmazásproxy összekötője számára a Kerberos-jegyek lekérését az Azure AD által hitelesített felhasználók számára. Ezt követően a kiszolgáló továbbítja a környezetet a célalkalmazás számára (ebben az esetben a SharePointban).

A KCD konfigurálásához hajtsa végre az alábbi lépéseket minden összekötő-gépen:

1. Jelentkezzen be tartományi rendszergazdaként egy tartományvezérlőre, majd nyissa meg Active Directory felhasználókat és számítógépeket.
1. Keresse meg az Azure AD proxy-összekötőt futtató számítógépet. Ebben a példában ez a SharePoint-kiszolgáló.
1. Kattintson duplán a számítógépre, majd válassza a **Delegálás** lapot.
1. Győződjön meg arról, hogy a delegálási beállítások úgy vannak beállítva, hogy a **számítógép csak a megadott szolgáltatások delegálására legyen megbízható**. Ezután válassza **a bármely hitelesítési protokoll használata** lehetőséget.
1. Válassza a **Hozzáadás** gombot, válassza a **felhasználók vagy számítógépek** lehetőséget, és keresse meg a SharePoint-alkalmazáskészlet fiókját. Példa: `Contoso\spapppool`.
1. Az SPN-lista listában válassza ki azt, amelyet korábban hozott létre a szolgáltatásfiók számára.
1. Kattintson **az OK gombra** , majd kattintson ismét **az OK** gombra a módosítások mentéséhez.
  
   ![Delegálási beállítások](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Most már készen áll a SharePointba való bejelentkezésre a külső URL-cím használatával és az Azure-beli hitelesítéssel.

## <a name="troubleshoot-sign-in-errors"></a>Bejelentkezési hibák elhárítása

Ha a helyhez való bejelentkezés nem működik, további információt kaphat az összekötő naplóiban található hibáról: az összekötőt futtató gépről nyissa meg az eseménynaplót, lépjen az **alkalmazások és szolgáltatások naplóba** a  >  **Microsoft**  >  **AadApplicationProxy**-  >  **összekötőt**, és vizsgálja meg a **felügyeleti** naplót.

## <a name="next-steps"></a>Következő lépések

* [Egyéni tartományok használata az Azure-ban AD Application Proxy](application-proxy-configure-custom-domain.md)
* [Az Azure AD Application Proxy-összekötők ismertetése](application-proxy-connectors.md)