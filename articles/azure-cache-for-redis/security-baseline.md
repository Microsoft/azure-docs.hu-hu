---
title: Azure-beli biztonsági alaptervek az Azure cache-hez a Redis
description: Azure-beli biztonsági alaptervek az Azure cache-hez a Redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 567652ec8da257e72aafa95e24af9f5680a2b6d3
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807954"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azure-beli biztonsági alaptervek az Azure cache-hez a Redis

A Redis készült Azure-gyorsítótár Azure-beli biztonsági alapterve olyan javaslatokat tartalmaz, amelyek segítségével javítható az üzemelő példány biztonsági helyzete.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: az Azure cache üzembe helyezése a Redis-példányon egy virtuális hálózaton (VNet) belül. A VNet a felhőben található magánhálózat. Ha egy Azure cache for Redis-példány VNet van konfigurálva, nem nyilvánosan címezhető, és csak a VNet lévő virtuális gépekről és alkalmazásokról érhető el.

A tűzfalszabályok a kezdő és a záró IP-címtartomány is megadhatók. A tűzfalszabályok konfigurálásakor csak a megadott IP-címtartományok kapcsolatai kapcsolódhatnak a gyorsítótárhoz.

A prémium szintű Azure cache Virtual Network támogatásának konfigurálása a Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Az Azure cache konfigurálása Redis tűzfalszabályok esetén:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutatás**: ha a Virtual Machines a Redis-példányhoz tartozó Azure cache-vel azonos virtuális hálózaton vannak telepítve, akkor a hálózati biztonsági csoportok (NSG-EK) segítségével csökkentheti az adatkiszűrése kockázatát. NSG-adatforgalmi naplók engedélyezése és naplók küldése Azure Storage-fiókba forgalom-ellenőrzés céljából. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

A NSG folyamat naplófájljainak engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure Virtual Network (VNet) üzembe helyezése fokozott biztonságot és elkülönítést biztosít az Azure cache Redis, valamint az alhálózatok, hozzáférés-vezérlési házirendek és egyéb funkciók számára a hozzáférés további korlátozásához. A VNet-ben üzembe helyezett Azure cache nem nyilvánosan címezhető, és csak a VNet lévő virtuális gépekről és alkalmazásokról érhető el.

A Redis-példányok esetében az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelem érdekében engedélyezze az Azure cache-hez társított virtuális hálózatok DDoS Protection szabványt. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

A prémium szintű Azure cache Virtual Network támogatásának konfigurálása a Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Azure DDoS Protection standard kezelése a Azure Portal használatával:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: Ha a virtuális gépeket ugyanabban a virtuális hálózatban telepítik, mint a Redis-példány Azure-gyorsítótárát, akkor a hálózati biztonsági csoportok (NSG-EK) használatával csökkentheti az adatkiszűrése kockázatát. NSG-adatforgalmi naplók engedélyezése és naplók küldése Azure Storage-fiókba forgalom-ellenőrzés céljából. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

A NSG folyamat naplófájljainak engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: Ha az Azure cache-t az Azure app Service-vagy számítási példányokon futó webalkalmazások Redis használja, helyezzen üzembe minden erőforrást egy azure-Virtual Network (VNet) belül, és gondoskodjon az Azure webalkalmazási TŰZFALLAL (WAF) a webes Application Gateway. Konfigurálja úgy a WAF, hogy "prevenciós módban" fusson. A megelőzési mód blokkolja a behatolásokat és a szabályok által észlelhető támadásokat. A támadó "403 jogosulatlan hozzáférés" kivételt kap, és a kapcsolat bezárult. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

Azt is megteheti, hogy az Azure piactéren olyan ajánlatot választ, amely támogatja az AZONOSÍTÓk/IP-címek funkciót a hasznos adatok ellenőrzése és/vagy anomália észlelési képességei alapján.

Az Azure WAF képességeinek megismerése:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Az Azure WAF üzembe helyezése:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok (NSG-EK) vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használja a Virtual Network szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Az alkalmazás biztonsági csoportjai (ASG-EK) is használhatók az összetett biztonsági konfiguráció egyszerűsítéséhez. A ASG lehetővé teszi a hálózati biztonság konfigurálását az alkalmazások struktúrájának természetes kiterjesztéseként, lehetővé téve a virtuális gépek csoportosítását és a hálózati biztonsági házirendek definiálását ezen csoportok alapján.

Virtuális hálózati szolgáltatás címkéi:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Alkalmazás biztonsági csoportjai:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure cache-hez kapcsolódó hálózati erőforrásokra vonatkozó szabványos biztonsági konfigurációk definiálása és implementálása Azure Policy Redis-példányokhoz. Használjon Azure Policy aliasokat a "Microsoft. cache" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre az Azure cache Redis-példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Olyan beépített szabályzat-definíciókat is használhat, mint például a következők:

Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve

DDoS Protection a standardot engedélyezni kell

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például a Azure Resource Manager-(ARM-) sablonokat, az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) és a szabályzatokat egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint létrehozása:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a Redis üzembe helyezéséhez az Azure cache-hez társított hálózati erőforrásokhoz használjon címkéket, hogy logikailag szervezze őket a besorolásba.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használatával figyelheti a hálózati erőforrás-konfigurációkat, és azonosíthatja a Redis-példányok Azure cache-hez kapcsolódó hálózati erőforrásainak változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

Az Azure Activity log eseményeinek megtekintése és beolvasása:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása a Azure Monitorban:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrásokhoz, például az Azure cache-hez használt időforrást a naplókban található időbélyegek Redis.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics-munkaterületre, Azure Event hub vagy Azure Storage-fiókba az archívumhoz. A tevékenységi naplók betekintést nyújtanak az Azure-gyorsítótárban végrehajtott műveletekre a Redis-példányok szintjén. Az Azure-tevékenység naplójának adatai segítségével meghatározhatja a "mi, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az Azure cache Redis-példányok esetében végzett vezérlési sík szintjén.

Az Azure-beli tevékenység naplójának diagnosztikai beállításainak engedélyezése: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics-munkaterületre, Azure Event hub vagy Azure Storage-fiókba az archívumhoz. A tevékenységi naplók betekintést nyújtanak az Azure-gyorsítótárban végrehajtott műveletekre a Redis-példányok szintjén. Az Azure-tevékenység naplójának adatai segítségével meghatározhatja a "mi, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az Azure cache Redis-példányok esetében végzett vezérlési sík szintjén.

Bár a mérőszámok elérhetők a diagnosztikai beállítások engedélyezésével, a naplózás az adatsíkon még nem érhető el az Azure cache for Redis számára.

Az Azure-beli tevékenység naplójának diagnosztikai beállításainak engedélyezése: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitor az Azure cache-hez társított log Analytics-munkaterületek naplózási megőrzési időszakának beállítása a szervezet megfelelőségi előírásai szerint Redis-példányokhoz.

Vegye figyelembe, hogy az adatsíkon végzett naplózás még nem érhető el az Azure cache for Redis számára.

Napló-megőrzési paraméterek beállítása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez, valamint számos más elemzéshez a Redis-hez készült Azure cache-hez gyűjtött tevékenység-naplózási adatok alapján.

Vegye figyelembe, hogy az adatsíkon végzett naplózás még nem érhető el az Azure cache for Redis számára.

Az Azure-beli tevékenység naplójának diagnosztikai beállításainak engedélyezése: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Azure-beli Tevékenységnaplók összegyűjtése és elemzése Log Analytics munkaterületen Azure Monitorban: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: beállíthatja, hogy a riasztásokat a Redis-példányokhoz tartozó Azure cache-hez kapcsolódó mérőszámok és tevékenységi naplók alapján fogadja. Azure Monitor lehetővé teszi a riasztások e-mail-értesítések küldését, egy webhook meghívását vagy egy Azure logikai alkalmazás meghívását.

Bár a mérőszámok elérhetők a diagnosztikai beállítások engedélyezésével, a naplózás az adatsíkon még nem érhető el az Azure cache for Redis számára.

Riasztások konfigurálása az Azure cache Redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; A Redis-hez készült Azure cache nem dolgoz fel és nem hoz létre kártevő-ellenes kapcsolódó naplókat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Redis-hez készült Azure cache nem dolgozza fel vagy hozza létre a DNS-sel kapcsolatos naplókat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ [: Security Control (identitás és Access Control](../security/benchmarks/security-control-identity-access-control.md)).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Active Directory (ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Redis Azure cache-hez való hozzáférésének szabályozása Azure Active Directory (ad) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával. 

Az Azure cache Redis-hez való hozzáférését hozzáférési kulcsok vezérlik. Ezeket a kulcsokat a gyorsítótárhoz csatlakozó ügyfelek használják, és bármikor újra létrehozhatók.

Nem ajánlott alapértelmezett jelszavakat létrehozni az alkalmazásba. Ehelyett a jelszavakat Azure Key Vaultban tárolhatja, majd a Azure Active Directory használatával lekérheti azokat.

Az Azure cache újragenerálása a Redis hozzáférési kulcsaihoz: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Az identitás és hozzáférés figyelésének Azure Security Center használata (előzetes verzió): https://docs.microsoft.com/azure/security-center/security-center-identity-access

A Azure Policy használata: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: a Redis készült Azure cache hozzáférési kulcsokat használ a felhasználók hitelesítéséhez, és nem támogatja az egyszeri bejelentkezést (SSO) az adatsík szintjén. Az Azure cache Redis-hez való hozzáférése REST APIon keresztül érhető el, és támogatja az egyszeri bejelentkezést. A hitelesítéshez állítsa be a kérések engedélyezési fejlécét egy Azure Active Directoryból beszerzett JSON Web Tokenra.

Az Azure cache ismertetése a Redis REST API: https://docs.microsoft.com/rest/api/redis/

Az SSO megismerése az Azure AD-vel: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory (AD) multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatainak követése.

Az MFA engedélyezése az Azure-ban: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identitás és hozzáférés figyelése Azure Security Centeron belül: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bevezetésére és konfigurálására konfigurált, multi-Factor Authentication (MFA) rendszerjogosultságú hozzáférési munkaállomások használata.

További tudnivalók az emelt szintű hozzáférésű munkaállomásokról:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

Privileged Identity Management (PIM) üzembe helyezése: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Az Azure AD kockázati észlelések ismertetése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutatás**: Azure Active Directory (ad) feltételes hozzáférés elnevezett helyeinek konfigurálása, hogy csak az IP-címtartományok vagy országok/régiók egyedi logikai csoportjaihoz férhessenek hozzá.

Elnevezett helyszínek konfigurálása az Azure-ban: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Az Azure AD-hitelesítés nem használható az Azure cache Redis adatsíkon való közvetlen elérésére, azonban az Azure AD hitelesítő adatai a vezérlési sík szintjén (például a Azure Portal) az Azure cache vezérléséhez használhatók az Redis hozzáférési kulcsaihoz.


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. 

Az Azure AD jelentéskészítés ismertetése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure Identity hozzáférési felülvizsgálatok használata: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: hozzáférhet Azure Active Directory (ad) bejelentkezési tevékenységhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik az Azure Sentinel vagy egy harmadik féltől származó Siem integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics belül is konfigurálhatja a kívánt naplózási riasztásokat.

Azure-beli tevékenység-naplók integrálása a Azure Monitorba: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

A fedélzeti Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a fiók bejelentkezési viselkedése a vezérlési síkon a Azure Active Directory (ad) Identity Protection és a kockázati észlelési funkciók használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

Az Azure AD kockázatos bejelentkezések megtekintése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: még nem érhető el; A Ügyfélszéf még nem támogatott az Azure cache for Redis esetében.

Ügyfélszéf által támogatott szolgáltatások listája:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. A Redis példányok Azure cache-t virtuális hálózat/alhálózat szerint kell elválasztani. Igény szerint a Redis tűzfal Azure cache szolgáltatásával definiálhat szabályokat úgy, hogy csak a megadott IP-címtartományok kapcsolatai kapcsolódhatnak a gyorsítótárhoz.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Az Azure cache üzembe helyezése a Redis egy vnet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Az Azure cache konfigurálása Redis tűzfalszabályok esetén:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: még nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure cache for Redis esetében.

A Microsoft kezeli az Azure cache mögöttes infrastruktúráját a Redis számára, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megakadályozására.

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Redis-hez készült Azure cache ALAPÉRTELMEZÉS szerint TLS-titkosítású kommunikációt igényel. A TLS 1,0, 1,1 és 1,2 verziók jelenleg támogatottak. Azonban a TLS 1,0 és a 1,1 egy olyan útvonalon van, amely az iparágra kiterjedő elavult, ezért a TLS 1,2-et használja, ha ez egyáltalán lehetséges. Ha az ügyféloldali kódtár vagy eszköz nem támogatja a TLS-t, akkor a titkosítatlan kapcsolatok engedélyezése a Azure Portal vagy a felügyeleti API-kon keresztül végezhető el. Olyan esetekben, ahol a titkosított kapcsolatok nem lehetségesek, ajánlott a gyorsítótár és az ügyfélalkalmazás virtuális hálózatra helyezése.

Az Azure cache Redis való átvitelének ismertetése:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

A vnet cache-forgatókönyvekben használt szükséges portok ismertetése:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure cache for Redis esetében. A bizalmas adatokat tartalmazó példányok címkézése, valamint a harmadik féltől származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az Azure cache-hez való hozzáférés vezérléséhez az Redis-vezérlési síkon (például Azure Portal). 

Az Azure RBAC konfigurálása:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kezeli az Azure cache mögöttes infrastruktúráját a Redis számára, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megakadályozására.

Az ügyfelek adatvédelem az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az Azure cache for Redis tárolja az ügyféladatokat a memóriában, és a Microsoft által megvalósított számos vezérlő erősen védi a memóriát, alapértelmezés szerint nincs titkosítva. Ha a szervezete megköveteli, a tartalom titkosítása az Azure cache-ben a Redis-hez való tárolás előtt.

Ha az Azure cache-t használja a "Redis adatmegőrzés" Redis-szolgáltatáshoz, a rendszer az Ön tulajdonában lévő és kezelt Azure Storage-fiókba küldi az adatgyűjtést. Az "új Azure cache for Redis" panel megőrzése a gyorsítótár létrehozásakor és a meglévő prémium gyorsítótárak erőforrás menüjében állítható be.

Az Azure Storage-ban tárolt adatai a 256 bites AES-titkosítással vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás nem tiltható le. A Microsoft által felügyelt kulcsokat használhatja a Storage-fiók titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti.

Az adatmegőrzés konfigurálása az Azure cache-ben a Redis esetében: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Az Azure Storage-fiókok titkosításának megismerése: https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Az Azure Customer adatvédelem ismertetése: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel a Redis és egyéb kritikus vagy kapcsolódó erőforrások esetében az Azure cache éles példányain végezheti el a módosításokat.

Riasztások létrehozása az Azure Activity log-eseményekhez:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: Azure Security Center ajánlásainak követése az Azure cache Redis-példányokhoz és kapcsolódó erőforrásokhoz való biztonságossá tételéhez.

A Microsoft a Redis-hez készült Azure cache-t támogató mögöttes rendszereken a biztonsági rések felügyeletét végzi.

Azure Security Center javaslatok ismertetése: https://docs.microsoft.com/azure/security-center/recommendations-reference

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Microsoft a Redis-hez készült Azure cache-t támogató mögöttes rendszereken a biztonsági rések felügyeletét hajtja végre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül.  Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

Lekérdezések létrehozása az Azure Resource Graph használatával: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC ismertetése: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: címkézés, felügyeleti csoportok és külön előfizetések használata, ahol szükséges, az Azure cache rendszerezése és nyomon követése Redis-példányokhoz és kapcsolódó erőforrásokhoz. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További Azure-előfizetések létrehozása: https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása: https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

Nem engedélyezett erőforrástípusok

Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph használatával:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

Nem engedélyezett erőforrástípusok

Engedélyezett erőforrástípusok

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása a következővel: Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: a felhasználók az Azure Resources Managerrel való interakcióra való képességének korlátozása parancsfájlok használatával

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók a Azure Resource Manager (ARM) szolgáltatással való interakciójának korlátozásához a "Microsoft Azure Management" alkalmazás "hozzáférés letiltása" beállításával.

Feltételes hozzáférés konfigurálása az ARM-hozzáférés blokkolásához:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: az Azure cache szabványos biztonsági konfigurációinak definiálása és implementálása a Redis-példányok Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. cache" névtérben egyéni szabályzatok létrehozásához az Azure cache Redis-példányok konfigurációjának naplózásához vagy érvényesítéséhez. Az Azure cache Redis-példányokhoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet, például:

Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve

Az elérhető Azure Policy aliasok megtekintése: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effektusok ismertetése: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: Ha a Redis-példányok és a kapcsolódó erőforrások esetében egyéni Azure Policy definíciókat vagy Azure Resource Manager sablonokat használ az Azure cache-hez, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Kód tárolása az Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Az Azure Repos dokumentációja: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. cache" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: Azure Policy aliasok használata a "Microsoft. cache" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. A Redis-példányok és a kapcsolódó erőforrások esetében az Azure cache konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [telepítés ha nem létezik] beállítást.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure-beli virtuális gépek vagy a Azure app Serviceon futó webalkalmazások számára a Redis-példányok Azure cache-hez való hozzáféréséhez használja a Managed Service Identityt a Azure Key Vault-mel, hogy egyszerűbbé és biztonságossá tegye az Azure cache-t a Redis titkos felügyeletéhez. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Integráció az Azure felügyelt identitásokkal:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

A Key Vault hitelesítése:

https://docs.microsoft.com/azure/key-vault/general/authentication

Key Vault hozzáférési szabályzatok kiosztása:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az Azure-beli virtuális gépek vagy a Azure app Serviceon futó webalkalmazások számára a Redis-példányok Azure cache-hez való hozzáféréséhez használja a Managed Service Identityt a Azure Key Vault-mel, hogy egyszerűbbé és biztonságossá tegye az Azure cache-t a Redis titkos felügyeletéhez. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja a HRE hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

Felügyelt identitások konfigurálása:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Integráció az Azure felügyelt identitásokkal:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

A hitelesítőadat-olvasó beállítása: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure App Service), azonban az nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure cache for Redis esetében), de nem fut az ügyfél tartalmán.

A nem számítási Azure-erőforrásokra feltöltött tartalom előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure cache for Redis esetében), de nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Redis megőrzésének engedélyezése. A Redis megőrzése lehetővé teszi a Redis-ben tárolt adatmegőrzést. Pillanatképeket és biztonsági mentést is készíthet, amelyeket hardverhiba esetén betölthet. Ez óriási előnyt jelent az alapszintű és a standard szint esetében, ahol az összes adat a memóriában tárolódik, és előfordulhat, hogy a gyorsítótár-csomópontok leállításakor hiba történt.

A Redis exportálásához használhatja az Azure cache-t is. Az Exportálás lehetővé teszi az Azure cache-ben tárolt adatexportálást a Redis-Redis kompatibilis RDB-fájl (ok) hoz. Ezzel a szolgáltatással áthelyezheti az adatok egyik Azure-gyorsítótárból a Redis-példányról egy másikra vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során létrejön egy ideiglenes fájl az Azure cache-t futtató virtuális gépen a Redis Server-példányhoz, és a fájl fel lesz töltve a kijelölt Storage-fiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződött be, a rendszer törli az ideiglenes fájlt.

A Redis megőrzésének engedélyezése:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Az Azure cache használata a Redis exportálásához:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: a Redis megőrzésének engedélyezése. A Redis megőrzése lehetővé teszi a Redis-ben tárolt adatmegőrzést. Pillanatképeket és biztonsági mentést is készíthet, amelyeket hardverhiba esetén betölthet. Ez óriási előnyt jelent az alapszintű és a standard szint esetében, ahol az összes adat a memóriában tárolódik, és előfordulhat, hogy a gyorsítótár-csomópontok leállításakor hiba történt.

A Redis exportálásához használhatja az Azure cache-t is. Az Exportálás lehetővé teszi az Azure cache-ben tárolt adatexportálást a Redis-Redis kompatibilis RDB-fájl (ok) hoz. Ezzel a szolgáltatással áthelyezheti az adatok egyik Azure-gyorsítótárból a Redis-példányról egy másikra vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során létrejön egy ideiglenes fájl az Azure cache-t futtató virtuális gépen a Redis Server-példányhoz, és a fájl fel lesz töltve a kijelölt Storage-fiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződött be, a rendszer törli az ideiglenes fájlt.

Ha a Redis-példányokhoz tartozó Azure gyorsítótárhoz tartozó hitelesítő adatok tárolására Azure Key Vault használ, ügyeljen arra, hogy a kulcsok rendszeres automatizált biztonsági mentéseit tárolja.

A Redis megőrzésének engedélyezése:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Az Azure cache használata a Redis exportálásához:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Key Vault kulcsok biztonsági mentése:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az Azure cache használata a Redis importálásához. Az importálással bármilyen felhőben vagy környezetben futó Redis-kiszolgálóról készíthet Redis-kompatibilis RDB-fájlokat, beleértve a Linuxon, a Windowson vagy bármely olyan felhőalapú szolgáltatón futó Redis, mint például a Amazon Web Services és mások. Az adatok importálása egyszerű módszer a gyorsítótár előre feltöltött adatokkal való létrehozására. Az importálási folyamat során az Azure cache for Redis betölti a RDB-fájlokat az Azure Storage-ból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

Rendszeresen tesztelje a Azure Key Vault titkos kódok adatvisszaállítását.

Az Azure cache használata a Redis importálásához:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Key Vault titkos kódok visszaállítása:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure cache a Redis exportálásának és a Redis megőrzésének Redis biztonsági mentéseit a kiválasztott Azure Storage-fiókban tárolja. Az Azure Storage-ban tárolt adatai a 256 bites AES-titkosítással vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás nem tiltható le. A Microsoft által felügyelt kulcsokat használhatja a Storage-fiók titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti.

Az Azure Storage-fiókok titkosításának megismerése: https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Microsoft

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy Incidensének anatómiája:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásához:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatának megtervezéséhez:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

A Azure Security Center biztonsági kapcsolattartó beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

Folyamatos exportálás konfigurálása:

https://docs.microsoft.com/azure/security-center/continuous-export

Riasztások továbbítása az Azure Sentinelbe:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

A Munkafolyamat-automatizálás és a Logic Apps konfigurálása:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: rendszeres penetrációs tesztelést végez az Azure-erőforrásokon, és gondoskodik az összes kritikus biztonsági vizsgálat 60 napon belüli szervizeléséről

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos további információkért tekintse meg a következő témakört: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További információ az [Azure biztonsági Alaptervekről](../security/benchmarks/security-baselines-overview.md)