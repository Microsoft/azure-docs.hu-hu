---
title: Azure AD biztonságos hibrid hozzáférés F5-mel | Microsoft Docs
description: F5 BIG-IP Access Policy Manager és Azure Active Directory integráció a biztonságos hibrid hozzáféréshez
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38b484bd67f3354132675f343dcc06bd7f9d48a0
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499812"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 BIG-IP Access Policy Manager és Azure Active Directory integráció a biztonságos hibrid hozzáféréshez

A mobilitás és a fejlődő veszélyforrások tájképének elterjedése további vizsgálatot tesz elérhetővé az erőforrás-hozzáférés és-irányítás terén, és az összes modernizációs program [megbízhatóságát](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) és középpontját helyezi üzembe.
A Microsoft és az F5 esetében ezt a digitális átalakítást általában egy több évre szóló, üzleti szempontból elérhetővé tettük, ami a modernizált kritikus fontosságú erőforrások kihagyása mellett lehetséges. Az F5 BIG-IP és Azure Active Directory Secure Hybrid Access (SHA) mögötti Genesis célja nem csupán a helyszíni alkalmazásokhoz való távoli hozzáférés javítása, hanem a sebezhető szolgáltatások általános biztonsági helyzetének megerősítése is.

A környezet szempontjából a kutatási becslések szerint a helyszíni alkalmazások 60-80%-a örökölt, vagy más szóval nem tud közvetlenül integrálni az Azure Active Directory (AD) szolgáltatással. Ugyanez a tanulmány azt is jelezte, hogy ezeknek a rendszereknek a nagy része az SAP, az Oracle, a SAGE és más, a kritikus szolgáltatásokat nyújtó, jól ismert munkaterhelések régebbi verzióin fut.

Az SHA ezt a vak helyet úgy kezeli, hogy lehetővé teszi, hogy a szervezetek továbbra is használják az F5-beruházásokat a superior hálózat és az alkalmazások kézbesítéséhez Az Azure AD-vel kombinálva a heterogén alkalmazások tájképét a modern Identity Control-síkon kezelheti.

A BIG-IP közzétett szolgáltatásokhoz való hozzáférés előzetes hitelesítését biztosító Azure AD számos előnnyel jár:

- Jelszó nélküli hitelesítés a [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview), az [MS hitelesítő](../user-help/user-help-auth-app-download-install.md), a [gyors identitású online (() kulcsok](../authentication/howto-authentication-passwordless-security-key.md)és a [tanúsítványalapú hitelesítés](../authentication/active-directory-certificate-based-authentication-get-started.md) ) használatával

- Preemptív karbantartással [feltételes hozzáférés](../conditional-access/overview.md) és [többtényezős hitelesítés (MFA)](../authentication/concept-mfa-howitworks.md)

- [Identity Protection](../identity-protection/overview-identity-protection.md) – adaptív vezérlés felhasználói és munkamenet-kockázati profilkészítéssel


- [Kiszivárgott hitelesítő adatok észlelése](../identity-protection/concept-identity-protection-risks.md)

- [Önkiszolgáló jelszó-visszaállítás (SSPR)](../authentication/tutorial-enable-sspr.md)

- [Partneri együttműködés](../governance/entitlement-management-external-users.md) – jogosultságok kezelése szabályozott vendég hozzáféréshez

- [Cloud app Security (CASB)](/cloud-app-security/what-is-cloud-app-security) – az alkalmazások felderítésének és ellenőrzésének befejezéséhez

- Fenyegetések figyelése – az [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) for Advanced Threat Analytics

- Az [Azure ad-portál](https://azure.microsoft.com/features/azure-portal/) – egyetlen, az identitás és a hozzáférés szabályozására szolgáló vezérlő síkja

## <a name="scenario-description"></a>Forgatókönyv leírása

Az Application Delivery Controller (ADC) és az SSL-VPN használatával a BIG-IP rendszer helyi és távoli hozzáférést biztosít minden típusú szolgáltatáshoz, többek között a következőkhöz:

- Modern és örökölt webalkalmazások

- Nem webalapú alkalmazások

- REST-és SOAP-alapú webes API-szolgáltatások

A helyi Traffic Manager (LTM) lehetővé teszi a szolgáltatások biztonságos közzétételét fordított proxy funkcióval. Ugyanakkor a kifinomult hozzáférési szabályzatok kezelője (APM) szélesebb körű képességekkel bővíti a BIG-IP-t, és lehetővé teszi az összevonás és az egyszeri bejelentkezés (SSO) használatát.

Az integráció az APM és az Azure AD közötti szabványos összevonási megbízhatósági kapcsolaton alapul, amely az [SSL-VPN-forgatókönyvet](f5-aad-password-less-vpn.md)magában foglaló legtöbb SHA-használati esettel közös. A Security Assertion Markup Language (SAML), a OAuth és a Open ID csatlakozási (OIDC) erőforrások nem kivételek, mert azok is biztonságossá válnak a távelérés számára. Olyan forgatókönyvek is lehetnek, amelyekben a BIG-IP megfulladási pontja az összes szolgáltatáshoz, köztük az SaaS-alkalmazásokhoz való hozzáférés zéró megbízhatóságú elérését eredményezi.

A BIG-IP azon képessége, hogy integrálható legyen az Azure AD-vel, ami lehetővé teszi, hogy a protokoll átváltásához a régi vagy nem Azure AD-integrációs szolgáltatásokat a modern vezérlőkkel, például a [jelszó nélküli hitelesítéssel](https://www.microsoft.com/security/business/identity/passwordless) és a [feltételes hozzáféréssel](../conditional-access/overview.md)kell védeni. Ebben a forgatókönyvben a BIG-IP továbbra is fordított proxyként teljesíti a szerepkörét, és az előhitelesítést és az engedélyezést az Azure AD-be, szolgáltatási alapon.

Az ábrán az 1-4-es lépések bemutatják a felhasználó, a BIG IP és az Azure AD közötti előtér-előhitelesítést a szolgáltató által kezdeményezett folyamat során. Az 5-6-as lépések azt mutatják be, hogy az APM munkamenet-bővítése és az egyszeri bejelentkezés az egyes háttér-szolgáltatásokhoz

![A képen a magas szintű architektúra látható](./media/f5-aad-integration/integration-flow-diagram.png)

| Lépés | Leírás |
|:------|:-----------|
| 1. | A felhasználó kiválasztja az alkalmazás ikonját a portálon, feloldja az SAML SP URL-címét (BIG-IP) |
| 2. | A BIG-IP átirányítja a felhasználót az SAML-IDENTITÁSSZOLGÁLTATÓ (Azure AD) az előzetes hitelesítéshez|
| 3. | Az Azure AD a feltételes hozzáférési házirendeket és a [munkamenet-vezérlőket](../conditional-access/concept-conditional-access-session.md) dolgozza fel az engedélyezéshez|
| 4. | A felhasználó visszairányítja az Azure AD által kiadott SAML-jogcímeket bemutató BIG-IP-re |
| 5. | A BIG-IP minden további munkamenet-információt kér, amely tartalmazza az [SSO](../hybrid/how-to-connect-sso.md) -t és a [szerepköralapú hozzáférés-vezérlést (RBAC)](../../role-based-access-control/overview.md) a közzétett szolgáltatáshoz |
| 6. | A BIG-IP továbbítja az ügyfél kérését a háttér-szolgáltatásnak

## <a name="user-experience"></a>Felhasználó felület

Függetlenül attól, hogy a közvetlen alkalmazott, az affiliate vagy a fogyasztó, a legtöbb felhasználó már ismeri az Office 365 bejelentkezési felületet, így a BIG-IP szolgáltatásokhoz való hozzáférés az SHA-n keresztül nagyban ismerős marad.

A felhasználók mostantól a  [MyApps](../user-help/my-apps-portal-end-user-access.md) -vagy [O365-Kezdőpanel](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) keretében KONSZOLIDÁLt Big-IP-alapú szolgáltatásokat is megtalálják, és az önkiszolgáló képességeket a szolgáltatások szélesebb körére, függetlenül attól, hogy milyen típusú eszközről vagy helyről van szó. A felhasználók továbbra is hozzáférhetnek a közzétett szolgáltatásokhoz közvetlenül a BIG-IPs tulajdonosi WebTop-portálon keresztül, ha az előnyben részesített. A kijelentkezéskor az SHA biztosítja, hogy a felhasználók munkamenete mindkét végén megszakadjon, a BIG-IP és az Azure AD-t, így biztosítva, hogy a szolgáltatások teljes mértékben védve legyenek a jogosulatlan hozzáféréstől.  

A képernyőképek az Azure AD alkalmazás-portálon találhatók, amelyekkel a felhasználók biztonságosan érhetik el a BIG-IP-alapú közzétett szolgáltatásaikat, és kezelhetik a fiókjuk tulajdonságait.  

![A képernyőképen a Woodgrove MyApps-gyűjtemény látható](media/f5-aad-integration/woodgrove-app-gallery.png)

![A képernyőképen a Woodgrove myaccounts önkiszolgáló lapja látható](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Elemzések és elemzések

A BIG-IP szerepe kritikus fontosságú minden vállalkozás számára, ezért a nagy IP-alapú példányok felügyeletét kell figyelni a közzétett szolgáltatások magas rendelkezésre állásának biztosítása érdekében, az SHA szintjén és a működés közben is.

Több lehetőség is létezik az események naplózására helyileg, vagy távolról egy biztonsági információ-és eseménykezelő (SIEM) megoldáson keresztül, amely lehetővé teszi a kikapcsolt tárolást és a telemetria feldolgozását. Az Azure AD és az SHA-specifikus tevékenységek monitorozására szolgáló hatékony megoldás a [Azure monitor](../../azure-monitor/overview.md) és az [Azure Sentinel](../../sentinel/overview.md)együttes használata, amely a következőket kínálja:

- A szervezet részletes áttekintése több felhőben és helyszíni helyeken, beleértve a BIG-IP-infrastruktúrát is

- Egyetlen vezérlő sík, amely az összes jelet kombinálja, elkerülve az összetett és a különböző eszközökre való támaszkodás

![A képen a figyelési folyamat látható](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Előfeltételek

Az F5 BIG-IP és az SHA-hez készült Azure AD integrálásának előfeltételei az alábbi előfeltételek:

- A következő platformok valamelyikén futó F5 BIG-IP-példány:

  - Fizikai készülék

  - Hypervisor Virtual Edition, például Microsoft Hyper-V, VMware ESXi, Linux KVM és Citrix hypervisor

  - Felhőbeli virtuális kiadás, mint például az Azure, a VMware, a KVM, a közösségi Xen, az MS Hyper-V, az AWS, a OpenStack és a Google Cloud

    A BIG-IP-példány tényleges helye lehet helyszíni vagy bármely támogatott felhőalapú platform, beleértve az Azure-t is, feltéve, hogy csatlakozik az internethez, a közzétett erőforrásokhoz és az egyéb szükséges szolgáltatásokhoz, például a Active Directoryhoz.  

- Egy aktív F5 BIG-IP APM-licenc az alábbi lehetőségek egyikével:

   - F5 BIG-IP® legjobb csomag (vagy)

   - F5 BIG-IP Access Policy Manager™ önálló licenc

   - F5 BIG-IP Access Policy Manager™ (APM) bővítmény licence meglévő BIG-IP F5 BIG-IP® helyi Traffic Manager™ (LTM)

   - 90 napos BIG-IP Access Policy Manager™ (APM) [próbaverziós licenc](https://www.f5.com/trial/big-ip-trial.php)

- Az Azure AD licencelése az alábbi lehetőségek egyikével:

   - Az Azure AD [ingyenes előfizetése](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) az SHA-nek a jelszó nélküli hitelesítéssel való megvalósításának minimális alapvető követelményeit biztosítja

   - A [prémium szintű előfizetés](https://azure.microsoft.com/pricing/details/active-directory/) minden további hozzáadott értéket tartalmaz, amelyek az előlapot ismertetik, beleértve a [feltételes hozzáférést](../conditional-access/overview.md), az [MFA](../authentication/concept-mfa-howitworks.md)-t és az [Identity Protectiont](../identity-protection/overview-identity-protection.md)

Az SHA megvalósításához nincs szükség korábbi élményre vagy F5 BIG-IP ismeretre, de javasoljuk, hogy ismerkedjen meg az F5 BIG-IP terminológiával. A F5's gazdag [tudásbázisa](https://www.f5.com/services/resources/glossary) is jó kiindulópont a Big-IP-ismeretek létrehozásának megkezdéséhez.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

A következő oktatóanyagok részletes útmutatást nyújtanak a BIG-IP és az Azure AD SHA leggyakoribb mintáinak megvalósításához:

- [F5 BIG-IP az Azure üzembe helyezése – útmutató](f5-bigip-deployment-guide.md)

- [F5 BIG-IP APM és Azure AD egyszeri bejelentkezés Kerberos-alkalmazásokhoz](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [F5 BIG-IP APM és Azure AD SSO a fejléc-alapú alkalmazásokhoz](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [Az F5 BIG-IP SSL-VPN védelme az Azure AD SHA-vel](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>További források

- [A jelszavak vége, jelszóval elmentve](https://www.microsoft.com/security/business/identity/passwordless)

- [Biztonságos hibrid hozzáférés Azure Active Directory](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [A Microsoft Zero megbízhatósági keretrendszere a távoli munka engedélyezéséhez](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Ismerkedés az Azure Sentinelrel](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Következő lépések

A meglévő BIG IP-infrastruktúra használatával vagy egy próbaverziós példány üzembe helyezésével érdemes lehet a (POC) SHA-igazolást futtatni. [A Big-IP Virtual Edition (ve) virtuális gép Azure-ba való üzembe helyezése](f5-bigip-deployment-guide.md) körülbelül 30 percet vesz igénybe, és ekkor a következőkre lesz szüksége:

- Teljes körűen védett platform a koncepciós SHA-próba modellezéséhez

- Üzem előtti példány, teljes mértékben biztonságos platform, amely az új BIG-IP Rendszerfrissítések és gyorsjavítások tesztelésére használható.

Ugyanakkor egy vagy két olyan alkalmazást kell azonosítania, amely a BIG-IP használatával történő közzétételre irányulhat, és az SHA használatával biztosít védelmet.  

Javasoljuk, hogy egy olyan alkalmazással kezdjen, amely még nem jelent meg BIG-IP-n keresztül, hogy elkerülje az üzemi szolgáltatások esetleges megszakadását. A cikkben említett irányelvek segítséget nyújtanak a különböző BIG IP-konfigurációs objektumok létrehozásához és az SHA beállításához szükséges általános eljárás megismeréséhez. Ha elkészült, akkor az összes többi új szolgáltatással azonosnak kell lennie, továbbá elegendő ismerettel kell rendelkeznie a meglévő BIG-IP közzétett szolgáltatások az SHA-be való átalakításához minimális erőfeszítéssel.

Az alábbi interaktív útmutató végigvezeti az SHA megvalósításának és a végfelhasználói élmény meglátásának folyamatán.

[![A képen látható az interaktív útmutató fedele](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
