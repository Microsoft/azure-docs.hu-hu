---
title: Event Grid Azure biztonsági alapterve
description: A Event Grid biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5423c26b15c5ba9fa84e5d823f75f3c82a8cb8b4
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968118"
---
# <a name="azure-security-baseline-for-event-grid"></a>Event Grid Azure biztonsági alapterve

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 1,0-es verziójának](../security/benchmarks/overview-v1.md) útmutatását alkalmazza Microsoft Azure Event Gridre. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Event Grid vonatkozó kapcsolódó útmutatás. A Azure Event Grid nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Azure Event Grid teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Event Grid biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: a privát végpontok segítségével közvetlenül a virtuális hálózatról a saját Event Grid témakörökbe és tartományba helyezheti át az eseményeket, anélkül, hogy a nyilvános interneten kellene haladnia. Amikor létrehoz egy privát végpontot a Event Grid témakörhöz vagy tartományhoz, biztonságos kapcsolatot biztosít a VNet lévő ügyfelek és a Event Grid erőforrás között. A magánhálózati végpont IP-címet kap a virtuális hálózat IP-címének tartományához. A magánhálózati végpont és a Event Grid szolgáltatás közötti kapcsolat biztonságos privát hivatkozást használ.

A Azure Event Grid a nyilvános IP-alapú hozzáférés-vezérlést is támogatja a témakörökben és tartományokban való közzétételhez. Az IP-alapú vezérlők használatával a közzétevőket egy témakörre vagy tartományra korlátozhatja, hogy csak a jóváhagyott gépek és felhőalapú szolgáltatások készlete legyen. Ez a szolgáltatás kiegészíti a Event Grid által támogatott hitelesítési mechanizmusokat. 

- [További részletek Event Grid privát végpontokról](./network-security.md#private-endpoints)

- [További információ a Event Grid IP-tűzfalról](./network-security.md#ip-firewall)

- [Azure Event Grid hálózati biztonság](network-security.md) 

- [Az Azure Private Link áttekintése](../private-link/private-link-overview.md)

- [Azure-beli hálózati biztonsági csoport](../virtual-network/network-security-groups-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózati védelmi javaslatok követése a Event Grid-erőforrások biztonságossá tételéhez az Azure-ban. Ha Azure-beli virtuális gépeket használ a Event Grid erőforrásaihoz való hozzáféréshez, engedélyezze a hálózati biztonsági csoport (NSG) folyamatának naplóit, és küldje el a naplókat a forgalmi naplózáshoz.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: beállíthatja a Event Grid erőforrás IP-tűzfalát úgy, hogy az csak az IP-címek és IP-címtartományok közül csak egy kiválasztott készletből korlátozza a hozzáférést a nyilvános interneten keresztül.

A privát végpontokat úgy is beállíthatja, hogy csak a kiválasztott virtuális hálózatokról korlátozza a hozzáférést.

Az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelem érdekében engedélyezze a virtuális hálózatokon DDoS Protection a standard szintű szolgáltatásokat. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel. További információért tekintse át a következő cikkeket: 

- [Privát végpontok konfigurálása Azure Event Grid témakörökhöz vagy tartományokhoz](configure-private-endpoints.md)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [További információ az Azure Security Center integrált veszélyforrások felderítéséről](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Ha Azure-beli virtuális gépeket használ a Event Grid erőforrásainak eléréséhez, engedélyezze a hálózati biztonsági csoport (NSG) folyamatábráit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Megjegyzés: a hálózati házirendek alapértelmezés szerint le vannak tiltva, ha a Event Grid privát végpontok létre lettek hozva, így előfordulhat, hogy a munkafolyamat nem működik.

Ha a rendellenes tevékenység kivizsgálásához szükséges, engedélyezze Network Watcher csomagok rögzítését.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: válasszon ki egy olyan ajánlatot az Azure piactéren, amely támogatja az azonosítók/IP-címek funkciót a hasznos adatok ellenőrzésére szolgáló funkciókkal.  Ha a hasznos adatok ellenőrzése nem követelmény, Azure Firewall a veszélyforrások felderítése is használható. Azure Firewall veszélyforrások felderítésére szolgáló szűrés a riasztások és/vagy az ismert kártékony IP-címek és tartományok felé irányuló, illetve azokból való adatforgalom letiltására szolgál. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy blokkolhatja a kártékony forgalmat.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a virtuális hálózatok olyan erőforrásaihoz, amelyeknek hozzáférést kell biztosítania a Azure Event Grid erőforrásaihoz, a hálózati biztonsági csoportokon vagy Azure Firewallokon a hálózati hozzáférés-vezérlés meghatározásához használja Virtual Network-szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például AzureEventGrid) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [A Azure Event Grid szolgáltatás címkéjének használata](./network-security.md#service-tags)

- [További információ a szolgáltatási címkék használatáról](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Azure Event Grid névterekhez társított hálózati erőforrásokra vonatkozó szabványos biztonsági konfigurációk definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. EventGrid" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Event Grid-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. 

Használhatja a Azure Event Gridhoz kapcsolódó beépített szabályzat-definíciókat is, például a következőket:

- Azure Event Grid tartományoknak privát hivatkozásokat kell használniuk

- Azure Event Grid témaköröknek az Azure privát hivatkozásait kell használniuk
- [beépített szabályzatok Event Grid erőforrásokhoz](../governance/policy/samples/built-in-policies.md#event-grid)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a Azure Event Grid erőforrásokhoz társított hálózati erőforrásokhoz használjon címkéket, hogy logikailag szervezze őket a besorolásba.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Event Grid kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor segítségével a Azure Event Grid által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterülettel kérdezheti le és végezheti el az elemzéseket, és használhat tárolási fiókokat a hosszú távú/archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események felügyeletét (SIEM).

- [Diagnosztikai naplók engedélyezése Azure Event Gridhoz](diagnostic-logs.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a diagnosztikai beállítások lehetővé teszik Event Grid felhasználók számára a közzétételi és kézbesítési hibák naplóinak rögzítését vagy megtekintését egy Storage-fiókban, egy Event hub-ban vagy egy log Analytics-munkaterületen.

- [Diagnosztikai naplók engedélyezése az Azure Event Grid-témakörökhöz vagy-tartományokhoz](enable-diagnostic-logs-topic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitor a szervezet megfelelőségi előírásai alapján állítsa be az Azure Event Grid erőforrásokhoz társított log Analytics-munkaterületek naplózásának megőrzési időtartamát.

- [Napló-megőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: elemezze és figyelje a rendellenes viselkedés naplóit, és rendszeresen tekintse át a Azure Event Grid eredményeit. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor és egy Log Analytics munkaterületet.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Diagnosztikai naplók engedélyezése az Azure Event Grid-témakörökhöz vagy-tartományokhoz](enable-diagnostic-logs-topic.md)

- [Azure Event Grid lekérdezések végrehajtása Log Analytics-munkaterületeken](diagnostic-logs.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Log Analytics lekérdezések első lépései](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Event Grid diagnosztikai beállításainak engedélyezése a közzétételi és kézbesítési hibák naplóihoz való hozzáféréshez. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek. A naplókat Log Analytics munkaterületre is elküldheti. A biztonsági naplókban és eseményekben található rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztáshoz használja a Azure Security Centert Log Analytics használatával. 

Riasztásokat is létrehozhat Azure Event Grid metrikák és tevékenység-naplózási műveletek esetén. Riasztásokat hozhat létre a közzétételi és kézbesítési mérőszámokon Azure Event Grid erőforrásokhoz (témakörökhöz és tartományokhoz) is. 

Emellett a Log Analytics munkaterületét az Azure Sentinelbe is bevezetheti, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását.

- [Diagnosztikai naplók engedélyezése Azure Event Grid témakörökhöz vagy tartományokhoz](enable-diagnostic-logs-topic.md)

- [Riasztások beállítása Azure Event Grid mérőszámokra és tevékenységi naplókra](set-alerts.md)

- [Event Grid diagnosztikai napló sémájának részletei](diagnostic-logs.md)

- [Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../azure-monitor/alerts/alerts-log.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; Azure Event Grid nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Azure Event Grid nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Event Grid lehetővé teszi, hogy a különböző felhasználók számára megadott hozzáférési szintet különböző felügyeleti műveletekkel, például esemény-előfizetések listázásával, újak létrehozásával és kulcsok létrehozásával szabályozhatja. Event Grid Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) használ. Event Grid támogatja a beépített szerepköröket és az egyéni szerepköröket.

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrásokhoz való hozzáférés kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal.

- [Event Grid erőforrásokhoz való hozzáférés engedélyezése](security-authorization.md)

- [Címtárbeli szerepkör beszerzése Azure Active Directoryban (Azure AD) a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Event Grid erőforrásokhoz való hozzáférés-kezelést Azure Active Directory (Azure ad) vezérli. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül.

Az igény szerinti hozzáférést Azure Active Directory (Azure AD) Privileged Identity Management és Azure Resource Manager használatával is engedélyezheti.

Event Grid engedélyezheti a felügyelt szolgáltatás identitását az Azure Event Grid-témakörökben vagy-tartományokban, és a használatával továbbíthatja az eseményeket olyan támogatott célhelyekre, mint például a Service Bus várólisták és témakörök, az Event hubok és a Storage-fiókok. A közös hozzáférésű aláírás (SAS) tokenje az események Azure Event Grid való közzétételére szolgál. Hozzon létre szabványos működési eljárást az esemény-hozzáférés, a továbbítás és a velük való közzététel köré.

- [Események kézbesítésének hitelesítése az eseménykezelők számára (Azure Event Grid)](security-authentication.md)

- [Közzétételi ügyfelek hitelesítése (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [További információ a Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: nem alkalmazható; Event Grid szolgáltatás nem támogatja az egyszeri bejelentkezést.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: nem alkalmazható; Event Grid szolgáltatás nem használ többtényezős hitelesítést.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: nem alkalmazható; nincsenek Event Grid forgatókönyvek emelt szintű hozzáférési munkaállomásokat igényelnek.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a Azure Active Directory (Azure ad) biztonsági jelentéseinek és figyelésének használata annak észlelésére, hogy a környezetben gyanús vagy nem biztonságos tevékenységek történnek-e. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: csak a jóváhagyott helyekről származó Azure-erőforrások kezelése

**Útmutató**: nem alkalmazható. A Event Grid nem használ Azure Active Directory (Azure AD) az esemény-közzétételi ügyfelek hitelesítéséhez; SAS-kulcsokon keresztül támogatja a hitelesítést.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Event Grid engedélyezheti a felügyelt szolgáltatás identitását az Azure Event Grid-témakörökben vagy-tartományokban, és a használatával továbbíthatja az eseményeket olyan támogatott célhelyekre, mint például a Service Bus várólisták és témakörök, az Event hubok és a Storage-fiókok. A közös hozzáférésű aláírás (SAS) tokenje az események Azure Event Grid való közzétételére szolgál. 

- [Események kézbesítésének hitelesítése az eseménykezelők számára (Azure Event Grid)](security-authentication.md)

- [Közzétételi ügyfelek hitelesítése (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure AD identitás-és hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

A naplók és a riasztások generálásához használjon Azure AD Privileged Identity Management (PIM), ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

- [Azure AD Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, naplózáshoz és kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

- [Azure-beli Tevékenységnaplók integrálása a Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) Identity Protection-funkciókkal konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem alkalmazható; A Event Grid szolgáltatás jelenleg nem támogatja az Ügyfélszéf.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.
 
 
 
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezeti típusokhoz és az adatérzékeny szintekhez. Korlátozhatja az alkalmazásaihoz és a vállalati környezetekhez igénybe veheti az Azure-erőforrásokhoz való hozzáférés szintjét. Az Azure-erőforrásokhoz való hozzáférést az Azure RBAC segítségével szabályozhatja.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a Microsoft által felügyelt mögöttes platformhoz a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésének és a kitettségének védelme érdekében jelentős hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: Azure Event Grid HTTPS-t igényel a közzétételhez, és támogatja a HTTPS-t az események webhook-végpontba való kézbesítéséhez. Az Azure Global-ban a Event Grid a TLS 1,1-es és 1,2-es verzióját is támogatja, de javasoljuk, hogy a 1,2 verziót használja. A 21Vianet által Kínában üzemeltetett nemzeti felhőkben, például a Azure Governmentban és az Azure-ban a Event Grid csak a TLS 1,2-es verzióját támogatja.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Event Grid számára. A harmadik féltől származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: a Azure Event Grid az Azure Active Directory (Azure ad) használatával támogatja a kérelmek Event Grid erőforrásokhoz való engedélyezését. Az Azure AD-vel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével engedélyeket biztosíthat egy rendszerbiztonsági tag számára, amely lehet egy felhasználó vagy egy egyszerű alkalmazás.

- [Event Grid erőforrásokhoz való hozzáférés engedélyezése](security-authorization.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel Azure Event Grid erőforrások és egyéb kritikus vagy kapcsolódó erőforrások éles példányain végezheti el a módosításokat.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: automatikus javítási megoldás üzembe helyezése harmadik féltől származó szoftverek címeihez

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.
 
 
 
- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a szervezeti igényeknek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

* Nem engedélyezett erőforrástípusok
* Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)
- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

* Nem engedélyezett erőforrástípusok
* Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure Active Directory (Azure ad) feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel együttműködni az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [ Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokban

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Event Grid szolgáltatás szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. EventGrid" névtérben egyéni szabályzatok létrehozásához a Azure Event Grid-szolgáltatások konfigurációjának naplózásához vagy érvénybe léptetéséhez.

A Azure Resource Manager lehetővé teszi a sablon exportálását JavaScript Object Notation (JSON), amelyet át kell tekinteni annak érdekében, hogy a konfigurációk megfeleljenek a szervezet biztonsági követelményeinek a központi telepítések előtt.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget. Emellett Azure Resource Manager-sablonokkal is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját. 

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha a Event Gridhoz vagy a kapcsolódó erőforrásokhoz egyéni Azure Policy definíciókat használ, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. EventGrid" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center használata az Azure-erőforrások alapkonfigurációjának vizsgálatához. Emellett az Azure-erőforrások konfigurációjának riasztására és naplózására Azure Policy is használhatja.

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: nem alkalmazható

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Event Grid közös hozzáférésű aláírási (SAS-) tokent használ az események közzétételéhez Event Grid témakörökben vagy tartományokban. SAS-tokenek generálása csak korlátozott időtartamú ablakban szükséges erőforrásokhoz való hozzáféréssel.

A felügyelt identitásokat a Azure Key Vaultekkel együtt használva egyszerűsítheti a Felhőbeli alkalmazások titkos kezelését.

- [Közzétételi ügyfelek hitelesítése (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Felügyelt identitások használata Azure-erőforrásokhoz](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault létrehozása](../key-vault/secrets/quick-create-portal.md)

- [Hitelesítés Key Vault](../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a Event Grid lehetővé teheti az Azure Event Grid-témakörök és-tartományok felügyelt szolgáltatásbeli identitásának engedélyezését. Használatával továbbíthatja az eseményeket olyan támogatott célhelyekre, mint a Service Bus várólisták és témakörök, az Event hubok és a Storage-fiókok.

- [Esemény kézbesítése felügyelt identitással](managed-service-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Event Grid), azonban nem az ügyfél tartalmán fut.

Az Ön felelőssége, hogy előzetesen beszkennelje a nem számítási Azure-erőforrásokra feltöltött tartalmakat. A Microsoft nem fér hozzá az ügyféladatok eléréséhez, így az Ön nevében nem végezhet kártevő-ellenőrzéseket az ügyfél-tartalmakon.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: Event Grid a metaadatok automatikus, nem csak az új, de az összes meglévő tartományra, témakörre és esemény-előfizetésre vonatkozó GeoDR-helyreállítási szolgáltatásával rendelkezik. Ha egy teljes Azure-régió leáll, Event Grid az összes eseménnyel kapcsolatos infrastruktúra-metaadatát szinkronizálja egy párosított régióba.

- [Kiszolgálóoldali földrajzi katasztrófa-helyreállítás Azure Event Grid](geo-disaster-recovery.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: Event Grid a metaadatok automatikus, nem csak az új, de az összes meglévő tartományra, témakörre és esemény-előfizetésre vonatkozó GeoDR-helyreállítási szolgáltatásával rendelkezik. Ha egy teljes Azure-régió leáll, Event Grid az összes eseménnyel kapcsolatos infrastruktúra-metaadatát szinkronizálja egy párosított régióba.

A Event Grid jelenleg nem támogatja az ügyfél által felügyelt kulcsokat. 

- [Kiszolgálóoldali földrajzi katasztrófa-helyreállítás Azure Event Grid](geo-disaster-recovery.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Event Grid a metaadatok automatikus, nem csak az új, de az összes meglévő tartományra, témakörre és esemény-előfizetésre vonatkozó GeoDR-helyreállítási szolgáltatásával rendelkezik. Ha egy teljes Azure-régió leáll, Event Grid az összes eseménnyel kapcsolatos infrastruktúra-metaadatát szinkronizálja egy párosított régióba.

A Event Grid jelenleg nem támogatja az ügyfél által felügyelt kulcsokat. 

- [Kiszolgálóoldali földrajzi katasztrófa-helyreállítás Azure Event Grid](geo-disaster-recovery.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze a Key Vault a védelem törlését és kiürítését. 
 

A Event Grid jelenleg nem támogatja az ügyfél által felügyelt kulcsokat. 

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: incidensek kifejlesztése útmutató a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az incidensek vizsgálatát követően. 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [A NIST számítógépes biztonsági incidensek kezelésével kapcsolatos útmutató a saját incidens-válasz tervének létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy a megbízhatósági Security Center hol van a megállapításban, illetve a riasztás kibocsátására használt analitikai módszer, valamint a riasztást eredményező tevékenység mögött rosszindulatú szándékkal rendelkező megbízhatósági szint.

 
 
 
 Emellett megadhatja a címkéket használó előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a hézagokat, majd szükség szerint módosítsa a választ.
 
 
 
- [ A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.
 
 
 
- [ A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a munkafolyamat-automatizálási szolgáltatás használata Azure Security Center a biztonsági riasztásokra és az Azure-erőforrások védelmére vonatkozó ajánlásokra adott válaszok automatikus elindítására.

- [A Munkafolyamat-automatizálás konfigurálása a Security Centerban](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
