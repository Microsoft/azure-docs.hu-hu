---
title: Azure biztonsági alapkonfiguráció automatizáláshoz
description: Az Automation biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ea75b9c1e8c7a1a242406b5a7f73db1b05c70276
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562205"
---
# <a name="azure-security-baseline-for-automation"></a>Azure biztonsági alapkonfiguráció automatizáláshoz

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Azure Automationre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Automation vonatkozó kapcsolódó útmutatás. A Azure Automation nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Azure Automation teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Automation biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: Azure Automation fiók még nem támogatja az Azure privát hivatkozását a szolgáltatáshoz való hozzáférés korlátozásához privát végpontokon keresztül. A runbookok az Azure-ban futtatott erőforrásokon végzett hitelesítés és Futtatás az Azure-beli homokozóban, valamint a megosztott háttérbeli erőforrások kihasználása, amelyet a Microsoft az egymástól való elkülönítésért felelős. a hálózatkezelésük nem korlátozott, és hozzáférhet a nyilvános erőforrásokhoz. A Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a magánhálózati hálózatkezeléshez a hibrid Runbook-feldolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

A runbookok további elkülönítéséhez használhatja az Azure Virtual Machines szolgáltatásban futó hibrid Runbook-feldolgozókat. Azure-beli virtuális gép létrehozásakor létre kell hoznia egy virtuális hálózatot (VNet), vagy egy meglévő VNet kell használnia, és konfigurálnia kell a virtuális gépeket egy alhálózattal. Győződjön meg arról, hogy az összes telepített alhálózat rendelkezik olyan hálózati biztonsági csoporttal, amely az alkalmazások megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel rendelkezik. A szolgáltatásra vonatkozó követelményekért tekintse meg az adott szolgáltatásra vonatkozó biztonsági javaslatot. Ha konkrét követelménye van, Azure Firewall is felhasználhatja a teljesítéshez.

- [Virtuális hálózatok és virtuális gépek az Azure-ban](../virtual-machines/network-overview.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)

- [Runbook végrehajtási környezet](./automation-runbook-execution.md#runbook-execution-environment)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a privát hálózatkezeléshez a hibrid Runbook-feldolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, győződjön meg arról, hogy az azokat tartalmazó alhálózat hálózati biztonsági csoporttal (NSG) van engedélyezve, és konfigurálja a folyamat naplóit a naplók továbbításához a forgalmi naplózáshoz. NSG-naplókat is továbbíthat egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Habár a NSG-szabályok és a felhasználó által megadott útvonalak nem vonatkoznak a privát végpontokra, a NSG és a kimenő kapcsolatok figyelési információi továbbra is támogatottak és használhatók.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a privát hálózatkezeléshez a hibrid Runbook-feldolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, engedélyezze az elosztott szolgáltatásmegtagadási (DDoS) szabvány szerinti védelmet a hibrid Runbook-feldolgozókat üzemeltető virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony IP-címekkel.  Konfigurálja Azure Firewall az egyes Virtual Network szegmenseken, és engedélyezze a fenyegetések felderítését, és konfigurálja a **riasztást, és tiltsa** le a kártékony hálózati forgalmat.

A Azure Security Center igény szerinti hálózati hozzáférésével korlátozhatja a Windows rendszerű virtuális gépeknek a jóváhagyott IP-címekre való kitettségét korlátozott ideig.  Emellett használjon Azure Security Center adaptív hálózattal kapcsolatos javaslatokat a NSG konfigurációkhoz a portok és a forrás IP-címek korlátozásához a tényleges forgalom és a veszélyforrások felderítése alapján.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

- [Azure Security Center adaptív hálózat megerősítésének ismertetése](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center az időponthoz tartozó hálózati Access Control ismertetése](../security-center/security-center-just-in-time.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: a Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a privát hálózatkezeléshez a hibrid Runbook-feldolgozók támogatásán túl, ez a vezérlő nem alkalmazható, ha hibrid feldolgozók nélkül használja a beépített szolgáltatást.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, akkor a NSG flow-naplókat rögzítheti egy Storage-fiókba, így a Runbook-feldolgozóként működő Azure-Virtual Machines flow-rekordokat hozhatja ki. A rendellenes tevékenység kivizsgálásakor engedélyezheti Network Watcher csomagok rögzítését, hogy a hálózati forgalom áttekinthető legyen a szokatlan és váratlan tevékenységekhez.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a privát hálózatkezeléshez a hibrid Runbook-feldolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

Ha az Azure Virtual Machines szolgáltatásban üzemeltetett hibrid Runbook-feldolgozókat használ, a Network Watcher és a nyílt forráskódú AZONOSÍTÓk eszközei által biztosított csomagok rögzítését kombinálhatja a hálózati behatolás észlelése érdekében a feldolgozó gépekkel kapcsolatos fenyegetések széles köre érdekében. Emellett a megfelelő módon telepítheti Azure Firewallokat a Virtual Network szegmensekre, és a fenyegetések felderítése engedélyezve van, és úgy van beállítva, hogy a kártékony hálózati forgalom a "riasztás és megtagadás" legyen.

- [Hálózati behatolás-észlelés végrehajtása Network Watcher és nyílt forráskódú eszközökkel](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: Virtual Network szolgáltatás-címkék használatával definiálhatja a hálózati biztonsági csoportokhoz vagy az Azure-ban konfigurált Azure Firewallhoz szükséges hálózati hozzáférés-vezérlést, amelyhez az Automation-erőforrásokhoz való hozzáférésre van szükség. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például GuestAndHybridManagement) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Azure Automation által használt hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával.

Az Azure-tervrajzok segítségével a nagy léptékű Azure-környezeteket is leegyszerűsítheti a főbb környezeti összetevők (például az Azure Resources Manager-sablonok, az Azure RBAC-vezérlők és a házirendek) egyetlen tervezet-definícióban való csomagolásával. A tervrajzot új előfizetésekre alkalmazhatja, és az irányítás és felügyelet finomhangolását a verziószámozás segítségével végezheti el.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a NSG és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz használható címkék használata. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti az erőforrás-konfigurációkat, és felderítheti a hálózati erőforrások módosításait. Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus erőforrások változásai megváltoznak.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplófájlok továbbítása Azure monitor naplókhoz Azure Automation erőforrások által generált biztonsági adatokat összesítve. A Azure Monitoron belül a naplók használatával kereshet és végezhet elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. A Azure Automation elküldheti a runbook feladatok állapotát, a feladatok adatfolyamait, az Automation állapotának konfigurációs adatait, az Update managementet, valamint a változások követését vagy leltározási naplóit a Log Analytics munkaterületre Ezek az információk láthatók a Azure Portal, Azure PowerShell és Azure Monitor naplók API-ból, amely lehetővé teszi az egyszerű vizsgálatok végrehajtását.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Azure Automation-feladat adatainak továbbítása az Azure Monitor-naplói felé](automation-manage-send-joblogs-log-analytics.md)

- [A DSC integrálása Azure Monitor naplókkal](automation-dsc-diagnostics.md)

- [A társított Log Analytics-munkaterület esetében támogatott régiók](how-to/region-mappings.md)

- [Update Management naplók lekérdezése](update-management/query-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure monitor engedélyezése a naplózási és a tevékenységi naplók eléréséhez, beleértve az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célhelyek és más hasznos elemeket. 

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

- [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Az Automation-fiókok adatmegőrzési részletei](./automation-managing-data.md#data-retention)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre és a rendszeres eredményekre vonatkozó naplók elemzése és figyelése. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használjon Azure Monitor naplózási lekérdezéseket.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [A Azure Monitor naplózási lekérdezéseinek ismertetése](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a biztonsági naplókban és eseményekben található rendellenes tevékenységekkel kapcsolatos figyelési és riasztási Azure Security Center használata Azure monitor használatával.

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a Azure Monitor naplózási információi alapján](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: harmadik féltől származó megoldás megvalósítása az Azure Marketplace-ről a DNS-naplózási megoldáshoz, amelyet a szervezeteknek szüksége van.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített rendszergazdai szerepkörök használata, amelyek explicit módon hozzárendelhetők, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez. Ha Automation-fiók futtató fiókokat használ a runbookok, győződjön meg arról, hogy az ilyen egyszerű szolgáltatásokat a leltárban is nyomon követik, mivel ezek gyakran megemelt jogosultságokkal rendelkeznek. Törölje a nem használt futtató fiókokat a kitett támadási felület minimalizálásához.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Futtató fiók vagy klasszikus futtató fiók törlése](delete-run-as-account.md)

- [Azure Automation futtató fiók kezelése](manage-runas-account.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure Automation fiók nem rendelkezik az alapértelmezett jelszavak fogalmával.  Az ügyfelek felelősek külső alkalmazások és piactér-szolgáltatásokért, amelyek a szolgáltatáson vagy a hibrid Runbook-feldolgozón futó alapértelmezett jelszavakat használhatják.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget. Ha Automation-fiók futtató fiókokat használ a runbookok, győződjön meg arról, hogy az ilyen egyszerű szolgáltatásokat a leltárban is nyomon követik, mivel ezek gyakran megemelt jogosultságokkal rendelkeznek. Ezek az identitások azokra a legalacsonyabb jogosultsági szintű engedélyekre terjednek ki, amelyekre szükségük van ahhoz, hogy a runbookok sikeresen elvégezze az automatikus folyamatot. Törölje a nem használt futtató fiókokat a kitett támadási felület minimalizálásához.

A Azure Active Directory (Azure AD) Privileged Identity Management a Microsoft-szolgáltatásokhoz és a Azure Resource Managerokhoz igénybe vehet egy igény szerinti és elég hozzáférési jogosultságot is.

- [További információ a Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

- [Futtató fiók vagy klasszikus futtató fiók törlése](delete-run-as-account.md)

- [Azure Automation futtató fiók kezelése](manage-runas-account.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: ha lehetséges, használja az egyszeri bejelentkezést Azure Active Directory (Azure ad) helyett, és ne konfigurálja az egyes önálló hitelesítő adatokat a szolgáltatásban. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata.

- [Egyszeri bejelentkezés az Azure AD-alkalmazásokba](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

- [Az Azure AD használata az Azure-beli hitelesítéshez](automation-use-azure-ad.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: a többtényezős hitelesítéssel konfigurált és a Azure Automation fiók erőforrásainak éles környezetben való bejelentkezésére és konfigurálására szolgáló mancsok használata. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól 

**Útmutató**: Azure Active Directory (Azure ad) kockázati észlelések használatával tekintheti meg a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket. Szükség esetén az ügyfél Azure Security Center kockázatkezelési riasztásokat is továbbíthat Azure Monitor és az egyéni riasztások/értesítések műveleti csoportok használatával történő konfigurálásához.

- [Azure Security Center kockázati észlelések ismertetése (gyanús tevékenység)](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Műveleti csoportok konfigurálása egyéni riasztásokhoz és értesítésekhez](../azure-monitor/alerts/action-groups.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről 

**Útmutató**: javasoljuk, hogy az elnevezett helyekkel rendelkező feltételes hozzáférés használatával engedélyezze a hozzáférést az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz. 

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza. Hibrid Runbook-feldolgozók használata esetén a futtató fiókok helyett felügyelt identitásokat is használhat a zökkenőmentes biztonságos engedélyek engedélyezéséhez.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

- [Runbook-hitelesítés használata felügyelt identitásokkal](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. Ha Automation-fiók futtató fiókokat használ a runbookok, gondoskodjon arról, hogy ezeket a szolgáltatásokat a leltárban is nyomon kövessék, mivel ezek gyakran megemelt jogosultságokkal rendelkeznek. Törölje a nem használt futtató fiókokat a kitett támadási felület minimalizálásához.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [Futtató fiók vagy klasszikus futtató fiók törlése](delete-run-as-account.md)

- [Azure Automation futtató fiók kezelése](manage-runas-account.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory (Azure ad) kockázati és Identity Protection-funkciókkal automatizált válaszokat konfigurálhat a hálózati erőforrás felhasználói identitásával kapcsolatos gyanús műveletekre. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: Azure Automation-fiókok esetében a Microsoft támogatási szolgálata a platform erőforrásaihoz tartozó metaadatokat egy másik eszköz használata nélkül is elérheti egy nyitott támogatási esetben.

Ha azonban az Azure Virtual Machines szolgáltatással támogatott hibrid Runbook-feldolgozókat használ, és egy külső félnek hozzá kell férnie az ügyféladatok eléréséhez (például egy támogatási kérelem során), az Azure Virtual Machines szolgáltatáshoz Ügyfélszéf (előzetes verzió) használatával tekintheti meg és hagyhatja jóvá vagy utasíthatja el az ügyféladatok hozzáférési kérelmeit.

- [A Ügyfélszéf ismertetése](../security/fundamentals/customer-lockbox-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure Automation-erőforrások nyomon követését. 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. A környezetek elkülönítése külön Automation-fiók erőforrásainak használatával. Az olyan erőforrásokat, mint a hibrid Runbook-feldolgozók, a megfelelő címkével elválasztva, a hálózati biztonsági csoporton (NSG) és a Azure Firewallon belül biztonságossá kell tennie. A bizalmas adatokat tároló vagy feldolgozó virtuális gépek esetében a házirend és eljárás (ok) bevezetésével kikapcsolhatja azokat, ha nincsenek használatban.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások vagy riasztások konfigurálása és a Azure Firewall megtagadása](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a hibrid Runbook-feldolgozói szolgáltatás használatakor az Azure Marketplace-ről származó külső megoldás kihasználható olyan hálózati környezetekben, amelyek figyelik a bizalmas információk jogosulatlan átvitelét, és blokkolja az adatátvitelt az adatbiztonsági szakemberek számára.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Győződjön meg arról, hogy az Azure-beli virtuális hálózatokban az Azure-erőforrásokhoz csatlakozó ügyfelek képesek legyenek egyeztetni a TLS 1,2-es vagy újabb verzióját. Azure Automation teljes mértékben támogatja és kikényszeríti a Transport Layer (TLS) 1,2 és az összes ügyfél-vagy újabb verziót minden külső HTPS-végponthoz (webhookok, DSC-csomópontok, hibrid runbook-feldolgozó).

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Azure Automation TLS 1,2 kényszerítés](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított bizalmas információk azonosítására egy külső féltől származó aktív felderítési eszköz használatával, beleértve a helyszíni vagy a távoli szolgáltatónál lévőket, valamint a szervezet bizalmas információinak leltározását.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozhatja a Azure Automation erőforrásokhoz való hozzáférést a beépített szerepkör-definíciók használatával, az Automation-erőforrásokhoz hozzáférő felhasználók számára a legkevésbé privilegizált vagy "elég" hozzáférési modellt követően. Hibrid Runbook-feldolgozók használata esetén a több-bérlős vagy hibrid Runbook-feldolgozók használata esetén a virtuális gépek felügyelt identitásait kell kihasználnia, ha a több-bérlős vagy a hibrid-feldolgozót egyaránt használja, ügyeljen arra, hogy megfelelően hatókörrel rendelkező Azure RBAC-engedélyeket alkalmazzon a Runbook

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [Runbook engedélyek a hibrid Runbook-feldolgozók számára](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Szerepköri engedélyek és biztonság kezelése](automation-role-based-access-control.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: a Azure Automation jelenleg nem teszi elérhetővé a több-bérlős runbook Worker virtuális gépeket, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, akkor egy harmadik féltől származó, gazdagépen alapuló adatvesztés-megelőzési megoldást kell használnia, amely kikényszeríti a hozzáférés-vezérlést az üzemeltetett hibrid Runbook Worker virtuális gépekhez.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az ügyfél által felügyelt kulcsok használata Azure Automationokkal. Azure Automation támogatja az ügyfél által felügyelt kulcsok használatát az összes felhasznált "biztonságos eszköz" titkosításához, például: hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók. A nem kívánt expozíció megelőzése érdekében a titkosított változókat az összes állandó változó keresési runbookok használja.

Hibrid Runbook-feldolgozók használata esetén a virtuális gépek virtuális lemezeinek tárolása a kiszolgálóoldali titkosítás vagy az Azure Disk Encryption (ADE) használatával történik. Az Azure Disk Encryption a Windows BitLocker szolgáltatásával titkosítja a felügyelt lemezeket az ügyfél által felügyelt kulcsokkal a vendég virtuális gépen. Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t, mivel lehetővé teszi a virtuális gépekhez tartozó operációsrendszer-típusok és-lemezképek használatát a Storage szolgáltatásban tárolt adattitkosítással.

- [Az Azure Managed Disks kiszolgálóoldali titkosítása](../virtual-machines/disk-encryption.md)

- [Azure Disk Encryption Windows rendszerű virtuális gépekhez](../virtual-machines/windows/disk-encryption-overview.md)

- [Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Felügyelt változók a Azure Automationban](shared-resources/variables.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Automation**:

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure Activity log használatával riasztásokat hozhat létre, amelyekkel a kritikus Azure-erőforrások, például a hálózati összetevők, a Azure Automation-fiókok és a runbookok változásaira kerül sor. 

- [Hálózati biztonsági csoport diagnosztikai naplózása](../private-link/private-link-overview.md#logging-and-monitoring)

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure Security Center ajánlásainak követése az Azure-erőforrások sebezhetőségi felmérésének végrehajtásával

- [Biztonsági javaslatok az Azure Security Centerben](../security-center/security-center-recommendations.md)

- [Security Center ajánlás referenciája](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a vizsgálati eredmények konzisztens időközönkénti exportálása és az eredmények összehasonlítása annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. Ha Azure Security Center által javasolt sebezhetőségi kezelési javaslatot használ, az ügyfél a kiválasztott megoldás portálján megtekintheti a korábbi vizsgálati adataikat.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata a felderített biztonsági rések szervizelésének rangsorolása érdekében.

- [A Azure Security Center biztonsági pontszámának megismerése](../security-center/secure-score-security-controls.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával kérdezheti le és derítheti fel az előfizetésekben található összes Azure Automation erőforrást. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban. 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ha szükséges, a Azure Automation erőforrások rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen. Törölje a nem használt futtató fiókokat a kitett támadási felület minimalizálásához.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Futtató fiók vagy klasszikus futtató fiók törlése](delete-run-as-account.md)

- [Azure Automation futtató fiók kezelése](manage-runas-account.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: létre kell hoznia egy leltárt a jóváhagyott Azure-erőforrásokról és jóváhagyott szoftverekről a számítási erőforrásokhoz a szervezeti igényeknek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával: 

- Nem engedélyezett erőforrástípusok 
- Engedélyezett erőforrástípusok 

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetéseken belüli erőforrásokat. Ez segíthet a magas biztonságú környezetekben, például a Storage-fiókokkal. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure Policy minta beépített Azure Automation](policy-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: az ügyfél meggátolhatja az erőforrások létrehozását és használatát Azure Policy az ügyfél vállalati iránymutatásainak megfelelően. A jogosulatlan erőforrások eltávolításához saját folyamatot is alkalmazhat. A Azure Automation-ajánlaton belül lehetőség van a PowerShell, illetve a runbookok által a portálon vagy a parancsmagokon keresztül elérhető Python-modulok telepítésére, eltávolítására és kezelésére. A nem jóváhagyott vagy régi modult el kell távolítani vagy frissíteni kell a runbookok.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Modul kezelése Azure Automation](shared-resources/modules.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférési szabályzatok használatával korlátozhatja a felhasználók azon képességét, hogy a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításának a nem biztonságos vagy nem jóváhagyott helyekről vagy eszközökről történő konfigurálásával korlátozza a Azure Resource Manager interakcióját. 

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy-aliasok használata egyéni szabályzatok létrehozásához a Azure Automation és a kapcsolódó erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. A beépített Azure Policy-definíciókat is használhatja.

Emellett Azure Resource Manager lehetősége van a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni, hogy a konfigurációk megfeleljenek/túllépik a szervezete biztonsági követelményeit.

Az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően Azure Security Center javaslatokat is alkalmazhat.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minta beépített Azure Automation](policy-reference.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: a Azure Automationhoz társított Azure-erőforrások biztonságos konfigurálásához Azure Resource Manager sablonok és Azure Policy használatával. Azure Resource Manager-sablonok az Azure-erőforrások üzembe helyezéséhez használt JSON-alapú fájlok, és az egyéni sablonokat biztonságosan kell tárolni és karbantartani egy adattárban. A verziókövetés integrációs funkciójával naprakészen tarthatja a runbookok az Automation-fiókban a verziókövetés adattárában található parancsfájlok használatával. Az Azure-erőforrások biztonságos beállításainak érvénybe léptetéséhez használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

- [Verziókövetés-integrálás használata](source-control-integration.md)

- [Azure Resource Manager sablonok létrehozásával kapcsolatos információk](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Automation-fiók üzembe helyezése Azure Resource Manager sablon használatával](/azure/automation/quickstart-create-automation-account-template)

- [Azure Policy minta beépített Azure Automation](policy-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, a Azure Resource Manager sablonokat és a kívánt állapotú konfigurációs parancsfájlokat. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van. A verziókövetés integrációs funkciójával naprakészen tarthatja a runbookok az Automation-fiókban a verziókövetés adattárában található parancsfájlok használatával.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Verziókövetés-integrálás használata](source-control-integration.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Az Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

- [Azure Policy minta beépített Azure Automation](policy-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: az Azure-erőforrások konfigurációjának riasztására és naplózására szolgáló Azure Policy használata, a szabályzat segítségével azonosíthatja a privát végponttal nem konfigurált erőforrásokat.

A hibrid Runbook Worker szolgáltatás használatakor a Azure Security Center kihasználhatja az Azure-beli virtuális gépek alapkonfigurációjának vizsgálatát.  Az automatikus konfiguráció további módszerei a következők: Azure Automation állapot konfigurálása.

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Azure Automation állapot konfigurációjának első lépései](automation-dsc-getting-started.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minta beépített Azure Automation](policy-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure Resource Manager használatával Azure Automation-fiókokat és kapcsolódó erőforrásokat telepíthet. A Azure Resource Manager lehetővé teszi olyan sablonok exportálását, amelyek biztonsági mentésként használhatók Azure Automation fiókok és kapcsolódó erőforrások visszaállításához. A Azure Automation használatával rendszeresen meghívhatja a Azure Resource Manager sablon exportálási API-ját.

A verziókövetés integrációs funkciójával naprakészen tarthatja a runbookok az Automation-fiókban a verziókövetés adattárában található parancsfájlok használatával.

- [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager sablon referenciája Azure Automation erőforrásokhoz](/azure/templates/microsoft.automation/allversions)

- [Automation-fiók létrehozása Azure Resource Manager sablon használatával](quickstart-create-automation-account-template.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Erőforráscsoportok – sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

- [Bevezetés a Azure Automationba](automation-intro.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Verziókövetés-integrálás használata](source-control-integration.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure Resource Manager használatával Azure Automation-fiókokat és kapcsolódó erőforrásokat telepíthet. A Azure Resource Manager lehetővé teszi olyan sablonok exportálását, amelyek biztonsági mentésként használhatók Azure Automation fiókok és kapcsolódó erőforrások visszaállításához. A Azure Automation használatával rendszeresen meghívhatja a Azure Resource Manager sablon exportálási API-ját. Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül. A runbookok a Azure Portal vagy a PowerShell használatával is exportálhatja parancsfájl-fájlokba.

- [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager sablon referenciája Azure Automation erőforrásokhoz](/azure/templates/microsoft.automation/allversions)

- [Automation-fiók létrehozása Azure Resource Manager sablon használatával](quickstart-create-automation-account-template.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Erőforráscsoportok – sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

- [Bevezetés a Azure Automationba](automation-intro.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Azure-beli adatbiztonsági mentés Automation-fiókokhoz](./automation-managing-data.md#data-backup)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az Azure Resource Manager-sablonok rendszeres időközönkénti üzembe helyezésének lehetősége egy elkülönített előfizetésre, ha szükséges. Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását.

- [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például Azure Resource Manager sablonokat. Az Azure DevOps felügyelt erőforrások védelme érdekében engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.

A verziókövetés integrációs funkciójával naprakészen tarthatja a runbookok az Automation-fiókban a verziókövetés adattárában található parancsfájlok használatával.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Verziókövetés-integrálás használata](source-control-integration.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal. 

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása 

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
