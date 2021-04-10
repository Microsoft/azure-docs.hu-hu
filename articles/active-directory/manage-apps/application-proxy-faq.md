---
title: Azure Active Directory Application Proxy gyakori kérdések
description: Megtudhatja, milyen válaszokat kaphat az Azure AD Application Proxy a belső, helyszíni alkalmazások távoli felhasználók számára történő közzétételére vonatkozó gyakori kérdésekre (GYIK).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: b76934d3f16ec5f87ed1380135d22bc5d943652c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779767"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Active Directory-(Azure AD-) alkalmazásproxy – gyakori kérdések

Ez az oldal válaszol a Azure Active Directory (Azure AD) alkalmazásproxy gyakori kérdéseire.

## <a name="enabling-azure-ad-application-proxy"></a>Az Azure AD Application Proxy engedélyezése

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Milyen licencre van szükség az Azure AD Application Proxy használatához?

Az Azure AD Application Proxy használatához rendelkeznie kell egy prémium szintű Azure AD P1 vagy P2 licenccel. További információ a licencelésről: [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>Mi történik az Azure AD Application Proxy a bérlőn, ha a licenc lejár?
Ha a licenc lejár, az alkalmazásproxy automatikusan le lesz tiltva. Az alkalmazás adatai egy évig lesznek mentve.

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Miért szürkén jelenik meg az "alkalmazásproxy engedélyezése" gomb?

Győződjön meg arról, hogy legalább egy prémium szintű Azure AD P1 vagy P2 licenccel rendelkezik, és telepítve van egy Azure AD Application Proxy-összekötő. Az első összekötő sikeres telepítése után az Azure AD Application Proxy szolgáltatás automatikusan engedélyezve lesz.

## <a name="connector-configuration"></a>Összekötő konfigurációja

### <a name="why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version"></a>Miért van még egy régebbi verziója, és ne legyen automatikusan frissítve a legújabb verzióra?

Ennek oka az lehet, hogy a frissítési szolgáltatás nem működik megfelelően, vagy ha nem érhető el új frissítés, amelyet a szolgáltatás telepíthet.

A frissítési szolgáltatás kifogástalan állapotú, ha fut, és nincsenek hibák rögzítve az eseménynaplóban (alkalmazások és szolgáltatások naplói – > Microsoft-> AadApplicationProxy-> Updater-> admin). 

> [!IMPORTANT]
> Az automatikus frissítés csak a főverziókat szabadítja fel. Ajánlott manuálisan frissíteni az összekötőt, ha szükséges. Nem várhat például egy nagyobb kiadásra, mert ki kell javítania egy ismert problémát, vagy új funkciót szeretne használni. Az új kiadásokkal kapcsolatos további információkért tekintse meg a kiadás típusát (letöltés, automatikus frissítés), hibajavításokat és új szolgáltatásokat, lásd [: Azure ad Application proxy: verzió kiadási előzmények](application-proxy-release-version-history.md).

Összekötő manuális frissítése:

-  Töltse le az összekötő legújabb verzióját. (Az Azure Portalon az Application proxy alatt találhatja meg. A hivatkozást az [Azure ad Application proxy: Version kiadási előzmények](application-proxy-release-version-history.md)című részében találja.
-   A telepítő újraindítja az Azure AD Application Proxy Connector Services szolgáltatást. Bizonyos esetekben szükség lehet a kiszolgáló újraindítására, ha a telepítő nem tudja lecserélni az összes fájlt. Ezért javasoljuk, hogy a frissítés megkezdése előtt zárjon be minden alkalmazást (azaz Eseménynapló).
-   Indítsa el a telepítőt. A frissítési folyamat gyors, és nem igényli a hitelesítő adatok megadását, és az összekötő nem lesz újra regisztrálva.

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Az alkalmazásproxy-összekötő szolgáltatások az alapértelmezettnél eltérő felhasználói környezetben futnak?

Nem, ez a forgatókönyv nem támogatott. Az alapértelmezett beállítások a következők:

- Microsoft HRE Application proxy-összekötő – WAPCSvc – hálózati szolgáltatás
- Microsoft AAD Application Proxy Connector Updater-WAPCUpdaterSvc-NT Authority\System

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>A globális rendszergazda vagy az alkalmazás-rendszergazda szerepkörrel rendelkező vendég felhasználó regisztrálja az összekötőt a (vendég) bérlőhöz?

Nem, jelenleg ez nem lehetséges. A regisztrációs kísérlet mindig a felhasználó otthoni bérlője számára történik.

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>A háttérben futó alkalmazás több webkiszolgálón üzemel, és felhasználói munkamenetek megőrzését igényli. Hogyan lehet elérni a munkamenetek megőrzését? 

Javaslatokért lásd: [az alkalmazásproxy-összekötők és-alkalmazások magas rendelkezésre állása és](application-proxy-high-availability-load-balancing.md)terheléselosztása.

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Támogatott-e a TLS-lezárás (TLS/HTTPS-ellenőrzés vagy gyorsítás) az összekötő-kiszolgálókról az Azure-ba irányuló forgalomhoz?

Az alkalmazásproxy-összekötő tanúsítvány alapú hitelesítést végez az Azure-ban. A TLS-megszakítás (TLS/HTTPS-ellenőrzés vagy gyorsítás) megszakítja ezt a hitelesítési módszert, és nem támogatott. Az összekötőtől az Azure-ba irányuló forgalomnak meg kell kerülnie a TLS-megszakítást végző eszközöket.  

### <a name="is-tls-12-required-for-all-connections"></a>A TLS 1,2 szükséges minden kapcsolathoz?
Igen. Ahhoz, hogy a legjobb titkosítást nyújtson ügyfeleinknek, az alkalmazásproxy szolgáltatás csak a TLS 1,2 protokollokra korlátozza a hozzáférést. Ezeket a módosításokat a 2019-es augusztus 31-ig fokozatosan kivezették és hatályba lépnek. Győződjön meg arról, hogy az összes ügyfél-kiszolgáló és böngésző-kiszolgáló kombináció frissítve van a TLS 1,2 használatára az alkalmazásproxy szolgáltatáshoz való csatlakozás fenntartása érdekében. Ezek közé tartoznak azok az ügyfelek, akikkel a felhasználók az Application proxyn keresztül közzétett alkalmazások elérését használják. Lásd: Felkészülés a [TLS 1,2-es verzióra az Office 365-ben](/microsoft-365/compliance/prepare-tls-1.2-in-office-365) hasznos referenciák és erőforrások.

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>A továbbító kiszolgáló (k) és a háttérrendszer-kiszolgáló között lehet-e továbbítási proxyt elhelyezni?
Igen, ez a forgatókönyv a 1.5.1526.0-összekötő verziótól kezdődően támogatott. Lásd: [a meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Létre kell hozni egy dedikált fiókot az összekötő Azure AD Application Proxy-beli regisztrálásához?

Nincs ok a következőre:. Minden globális rendszergazda vagy alkalmazás-rendszergazdai fiók működni fog. A telepítés során megadott hitelesítő adatok nem használhatók a regisztrációs folyamat után. Ehelyett egy tanúsítványt állítanak ki az összekötőnek, amely az adott pontról történő hitelesítéshez használatos.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Hogyan figyelhető meg az Azure AD Application Proxy-összekötő teljesítménye?

A Teljesítményfigyelő-számlálók a-összekötővel együtt települnek. Megtekintheti őket:  

1. Kattintson a **Start** gombra, írja be a "Perfmon" kifejezést, majd nyomja le az ENTER billentyűt.
2. Válassza a **Teljesítményfigyelő** lehetőséget, majd kattintson a zöld **+** ikonra.
3. Adja hozzá a figyelni kívánt **Microsoft HRE** -alkalmazásproxy összekötő számlálóit.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Az Azure AD Application Proxy-összekötőnek ugyanazon az alhálózaton kell lennie, mint az erőforrásnak?

Az összekötőnek nem kell ugyanahhoz az alhálózathoz tartoznia. Azonban szükség van a névfeloldásra (DNS, hosts file) az erőforrásra és a szükséges hálózati kapcsolatra (az erőforrás útválasztása, az erőforráson megnyitott portok stb.). Javaslatokért lásd: [hálózati topológia szempontjai Azure Active Directory Application proxy használatakor](application-proxy-network-topology.md).

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>A Windows Server mely verziói telepíthetnek összekötőt?

Az alkalmazásproxy használatához a Windows Server 2012 R2 vagy újabb verzió szükséges. A Windows Server 2019-es HTTP2 esetében jelenleg korlátozás van érvényben. Ahhoz, hogy sikeresen használhassa az összekötőt a Windows Server 2019-on, hozzá kell adnia a következő beállításkulcsot, és újra kell indítania a kiszolgálót:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
```

## <a name="application-configuration"></a>Alkalmazáskonfiguráció

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>Hibaüzenetet kapok egy érvénytelen tanúsítványról vagy a lehetséges helytelen jelszóról

Az SSL-tanúsítvány feltöltése után a portálon "érvénytelen tanúsítvány, lehetséges helytelen jelszó" üzenet jelenik meg.

Íme néhány tipp a hiba elhárításához:
- A tanúsítvánnyal kapcsolatos problémák keresése. Telepítse a helyi számítógépre. Ha nem tapasztal problémát, akkor a tanúsítvány jó.
- Győződjön meg arról, hogy a jelszó nem tartalmaz speciális karaktereket. A teszteléshez a jelszó csak a 0-9, A-Z és a-z karaktereket tartalmazza.
- Ha a tanúsítvány Microsoft szoftveres kulcstároló-szolgáltatóval lett létrehozva, akkor az RSA-algoritmust kell használni.

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Milyen hosszúságú az alapértelmezett és a "hosszú" háttérbeli időtúllépés? Ki lehet terjeszteni az időtúllépést?

Az alapértelmezett hossz 85 másodperc. A "Long" beállítás 180 másodperc. Nem lehet kiterjeszteni az időtúllépési korlátot.

### <a name="can-a-service-principal-manage-application-proxy-using-powershell-or-microsoft-graph-apis"></a>Az egyszerű szolgáltatásnév a PowerShell vagy a Microsoft Graph API-k használatával kezelhető az alkalmazásproxy?

Nem, ez jelenleg nem támogatott.

### <a name="what-happens-if-i-delete-cwap_authsecret-the-client-secret-in-the-app-registration"></a>Mi történik, ha törölem CWAP_AuthSecret (az ügyfél titkos kulcsát) az alkalmazás regisztrálásakor?

Az ügyfél titkos kulcsát (más néven *CWAP_AuthSecret*) automatikusan hozzáadja az alkalmazás-objektumhoz (alkalmazás-regisztráció) az Azure ad Application proxy-alkalmazás létrehozásakor.

Az ügyfél titkos kulcsa egy évig érvényes. A rendszer automatikusan létrehoz egy új egyéves ügyfél-titkos kulcsot, mielőtt az aktuálisan érvényes ügyfél titkos kulcsa lejár. Három CWAP_AuthSecret ügyfél titkos kulcsát mindig az Application objektum tárolja. 

> [!IMPORTANT]
> CWAP_AuthSecret az Azure-AD Application Proxy előhitelesítésének törlése. Ne törölje CWAP_AuthSecret.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Hogyan módosítja az alkalmazás betöltését a kezdőlapon?

Az alkalmazás-regisztrációk lapon módosíthatja a Kezdőlap URL-címét a céloldal kívánt külső URL-címére. A megadott oldal betöltődik, amikor az alkalmazást a saját alkalmazások vagy az Office 365 portálról indítják el. A konfigurációs lépésekért lásd: [Egyéni Kezdőlap beállítása közzétett alkalmazásokhoz az Azure ad Application proxy használatával](./application-proxy-configure-custom-home-page.md)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Csak az IIS-alapú alkalmazások közzétehetők? Mi a helyzet a nem Windows webkiszolgálókon futó webalkalmazásokkal? Telepíteni kell az összekötőt olyan kiszolgálóra, amelyen telepítve van az IIS?

Nem, nincs IIS-követelmény a közzétett alkalmazásokhoz. A Windows Serveren kívül más kiszolgálókon futó webalkalmazásokat is közzétehet. Előfordulhat azonban, hogy nem tudja használni az előhitelesítést egy nem Windows Serverrel, attól függően, hogy a webkiszolgáló támogatja-e az egyeztetést (Kerberos-hitelesítés). Az IIS nem szükséges azon a kiszolgálón, amelyen az összekötő telepítve van.

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>Beállítható az alkalmazásproxy az HSTS-fejléc hozzáadásához?
Az alkalmazásproxy nem adja hozzá automatikusan a HTTP Strict-Transport-Security fejlécet a HTTPS-válaszokhoz, de megtartja a fejlécet, ha a közzétett alkalmazás által küldött eredeti válaszban szerepel. A funkció engedélyezésére vonatkozó beállítás az ütemterven található. Ha olyan előzetes verziót szeretne, amely lehetővé teszi a hozzáadását a válaszokhoz, a részletekért tekintse meg a aadapfeedback@microsoft.com következőt:.

## <a name="integrated-windows-authentication"></a>Beépített Windows-hitelesítés

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Mikor érdemes a PrincipalsAllowedToDelegateToAccount módszert használni a Kerberos által korlátozott delegálás (KCD) beállításakor?

A PrincipalsAllowedToDelegateToAccount metódus akkor használatos, ha az összekötő-kiszolgálók a webalkalmazás-szolgáltatási fióktól eltérő tartományba tartoznak. Az erőforrás-alapú korlátozott delegálás használatát igényli.
Ha az összekötő-kiszolgálók és a webalkalmazás-szolgáltatásfiók ugyanabban a tartományban található, Active Directory felhasználókkal és számítógépekkel konfigurálja a delegálási beállításokat az egyes összekötők számítógépein, lehetővé téve számukra a cél SPN-re való delegálást.

Ha az összekötő-kiszolgálók és a webalkalmazás-szolgáltatás fiókja különböző tartományokban található, akkor az erőforrás-alapú delegálás használatos. A delegálási engedélyek a cél webkiszolgálón és a webalkalmazás-szolgáltatási fiókon vannak konfigurálva. Ez a korlátozott delegálási módszer viszonylag új. A metódust a Windows Server 2012-ben vezették be, amely támogatja a tartományok közötti delegálást azáltal, hogy az erőforrás (webszolgáltatás) tulajdonosa szabályozhatja, hogy mely számítógép-és szolgáltatásfiókok tudják delegálni. Ehhez a konfigurációhoz nincs szükség felhasználói felületre, ezért a PowerShellt kell használnia.
További információkért tekintse meg a [Kerberos által korlátozott delegálás az Application proxyval](https://aka.ms/kcdpaper)című tanulmányt.

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>Az NTLM-hitelesítés együttműködik az Azure AD Application Proxy?

Az NTLM-hitelesítés nem használható előhitelesítés vagy egyszeri bejelentkezési módszerként. Az NTLM-hitelesítés csak akkor használható, ha közvetlenül az ügyfél és a közzétett webes alkalmazás között lehet egyeztetni. Az NTLM-hitelesítés használata általában egy bejelentkezési üzenet megjelenítését okozza a böngészőben.

### <a name="can-i-use-the-logon-identity-on-premises-user-principal-name-or-on-premises-sam-account-name-in-a-b2b-iwa-single-sign-on-scenario"></a>Használhatom a "helyszíni felhasználó egyszerű neve" vagy a "helyszíni SAM-fiók neve" kifejezést egy B2B IWA egyszeri bejelentkezési forgatókönyvben?

Nem, ez nem fog működni, mert az Azure AD-beli vendég felhasználó nem rendelkezik a fent említett bejelentkezési identitások által igényelt attribútummal.

Ebben az esetben az "egyszerű felhasználónév" lesz tartalék. A B2B-forgatókönyvről további információért olvassa el a [B2B-felhasználók Azure ad-beli hozzáférésének engedélyezése a helyszíni alkalmazásokhoz](../external-identities/hybrid-cloud-to-on-premises.md)című témakört.

## <a name="pass-through-authentication"></a>Átmenő hitelesítés

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Használhatok feltételes hozzáférési szabályzatokat az átmenő hitelesítéssel közzétett alkalmazások esetében?

A feltételes hozzáférési szabályzatok csak az Azure AD-ben sikeresen hitelesített felhasználók számára lettek kikényszerítve. Az átmenő hitelesítés nem aktiválja az Azure AD-hitelesítést, így a feltételes hozzáférési szabályzatok nem kényszeríthető ki. Az átmenő hitelesítéssel az MFA-házirendeket a helyszíni kiszolgálón, ha lehetséges, vagy az Azure AD Application Proxy használatával történő előhitelesítés engedélyezésével kell megvalósítani.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Közzétehetek egy webalkalmazást az ügyféltanúsítvány-alapú hitelesítési követelmény használatával?

Nem, ez a forgatókönyv nem támogatott, mert az alkalmazásproxy leállítja a TLS-forgalmat.  

## <a name="remote-desktop-gateway-publishing"></a>Távoli asztali átjáró közzététel

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Hogyan tehetek közzé Távoli asztali átjáró az Azure AD Application Proxyon?

Tekintse át a [Távoli asztal közzététele az Azure ad Application proxy](application-proxy-integrate-with-remote-desktop-services.md)-ban című témakört.

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Használhatom a Kerberos által korlátozott delegálást (egy Sign-On – integrált Windows-hitelesítés) a Távoli asztali átjáró közzétételi forgatókönyvben?

Nem, ez a forgatókönyv nem támogatott.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>A felhasználók nem használják az Internet Explorer 11-et, és az előhitelesítési forgatókönyv nem működik. Ez várható?

Igen, a várt érték. Az előhitelesítési forgatókönyvhöz egy olyan ActiveX-vezérlő szükséges, amely harmadik féltől származó böngészőkben nem támogatott.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Támogatott-e a Távoli asztal Web Client (HTML5)?

Igen, ez a forgatókönyv jelenleg nyilvános előzetes verzióban érhető el. Tekintse át a [Távoli asztal közzététele az Azure ad Application proxy](application-proxy-integrate-with-remote-desktop-services.md)-ban című témakört.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Miután konfiguráltam az előhitelesítési forgatókönyvet, észrevettem, hogy a felhasználónak kétszer kell hitelesítenie magát: először az Azure AD bejelentkezési űrlapján, majd a RDWeb bejelentkezési űrlapján. Ez várható? Hogyan csökkentheti ezt az egyszeri bejelentkezést?

Igen, a várt érték. Ha a felhasználó számítógépe Azure AD-hez csatlakozik, a felhasználó automatikusan bejelentkezik az Azure AD-be. A felhasználónak csak a RDWeb bejelentkezési űrlapján kell megadnia a hitelesítő adatait.

## <a name="sharepoint-publishing"></a>SharePoint-közzététel

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Hogyan tehetem közzé a SharePointot az Azure AD Application Proxyon?

Tekintse meg a [távoli hozzáférés engedélyezése a sharepointhoz az Azure ad Application proxy](application-proxy-integrate-with-sharepoint-server.md)használatával című témakört.

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Használhatom a SharePoint Mobile alkalmazást (iOS/Android) a közzétett SharePoint-kiszolgáló eléréséhez?

A [SharePoint Mobile alkalmazás](/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) jelenleg nem támogatja a Azure Active Directory előhitelesítést.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Közzététel Active Directory összevonási szolgáltatások (AD FS) (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Használhatom az Azure AD Application Proxy AD FS proxyként (például a webalkalmazás-proxyt)?

Nem. Az Azure AD Application Proxy úgy lett kialakítva, hogy az Azure AD-vel működjön, és nem teljesíti a követelményeket AD FS proxyként való működéshez.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense-and-remote-desktop-web-client-html5"></a>A WebSocket támogatja a QlikSense és a Távoli asztal Web Client (HTML5) szolgáltatáson kívüli alkalmazásokhoz való munkát?

A WebSocket protokoll támogatása jelenleg még nyilvános előzetes verzióban érhető el, és előfordulhat, hogy más alkalmazásokhoz nem működik. Néhány ügyfél vegyes sikerrel járt a WebSocket protokoll használatával más alkalmazásokkal. Ha teszteli az ilyen forgatókönyveket, szeretnénk hallani az eredményeket. Kérjük, küldje el nekünk visszajelzését a következő címen: aadapfeedback@microsoft.com .

A Windows felügyeleti központban (WAC) található szolgáltatások (eseménynaplók, PowerShell és Távoli asztali szolgáltatások) nem működnek az Azure AD Application Proxy használatával.

## <a name="link-translation"></a>Fordítás csatolása

### <a name="does-using-link-translation-affect-performance"></a>A hivatkozás fordítása hatással van a teljesítményre?

Igen. A hivatkozás fordítása hatással van a teljesítményre. Az alkalmazásproxy szolgáltatás megkeresi az alkalmazást a hardcoded-hivatkozásokra, és lecseréli azokat a megfelelő, közzétett külső URL-címekre, mielőtt azokat bemutatják a felhasználónak. 

A legjobb teljesítmény érdekében az azonos belső és külső URL-címek használatát javasoljuk [Egyéni tartományok](./application-proxy-configure-custom-domain.md)konfigurálásával. Ha az egyéni tartományok használata nem lehetséges, a saját alkalmazások biztonságos bejelentkezési bővítmény vagy a mobil Microsoft Edge böngésző használatával javíthatja a hivatkozások fordítási teljesítményét. Lásd: az [Azure ad Application proxy közzétett alkalmazásaihoz tartozó hardcoded-hivatkozások átirányítása](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Helyettesítő karakterek

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Hogyan helyettesítő karakterek használatával két, azonos egyéni tartománynévvel rendelkező alkalmazást, de különböző protokollokat is közzétehet, egyet a HTTP-re, egyet a HTTPS-re?

Ez a forgatókönyv nem támogatott közvetlenül. A forgatókönyv beállításai a következők:

1. Tegye közzé mind a HTTP-, mind a HTTPS-URL-címet külön alkalmazásként egy helyettesítő karakterrel, de adjon nekik egy másik egyéni tartományt. Ez a konfiguráció úgy fog működni, mert eltérő külső URL-címekkel rendelkeznek.

2. A HTTPS URL-cím közzététele helyettesítő alkalmazás használatával. Tegye közzé a HTTP-alkalmazásokat az alábbi alkalmazásproxy PowerShell-parancsmagok használatával:
   - [Alkalmazásproxy-alkalmazások kezelése](/powershell/module/azuread/#application_proxy_application_management&preserve-view=true)
   - [Alkalmazásproxy-összekötő kezelése](/powershell/module/azuread/#application_proxy_connector_management&preserve-view=true)
