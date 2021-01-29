---
title: Azure Automation Azure biztonsági alapterve
description: Azure biztonsági alapkonfiguráció automatizáláshoz
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 79282e99894bccad4f3c265cdffe4bee2e465950
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052753"
---
# <a name="azure-security-baseline-for-automation"></a>Azure biztonsági alapkonfiguráció automatizáláshoz

Az Automation Azure Security alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: az [Azure biztonsági alaptervek áttekintése](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: Azure Automation fiók még nem támogatja az Azure privát hivatkozását a szolgáltatáshoz való hozzáférés korlátozásához privát végpontokon keresztül. A runbookok az Azure-ban futtatott erőforrásokon végzett hitelesítés és Futtatás az Azure-beli homokozóban, valamint a megosztott háttérbeli erőforrások kihasználása, amelyet a Microsoft az egymástól való elkülönítésért felelős. a hálózatkezelésük nem korlátozott, és hozzáférhet a nyilvános erőforrásokhoz. A Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a magánhálózati hálózatkezeléshez a hibrid Runbook-feldolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

A runbookok további elkülönítéséhez használhatja az Azure Virtual Machines szolgáltatásban futó hibrid Runbook-feldolgozókat. Azure-beli virtuális gép létrehozásakor létre kell hoznia egy virtuális hálózatot (VNet), vagy egy meglévő VNet kell használnia, és konfigurálnia kell a virtuális gépeket egy alhálózattal. Győződjön meg arról, hogy az összes telepített alhálózat rendelkezik olyan hálózati biztonsági csoporttal, amely az alkalmazások megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel rendelkezik. A szolgáltatásra vonatkozó követelményekért tekintse meg az adott szolgáltatásra vonatkozó biztonsági javaslatot.

Ha konkrét követelménye van, Azure Firewall is felhasználhatja a teljesítéshez.

* [Virtuális hálózatok és virtuális gépek az Azure-ban](../virtual-machines/network-overview.md)

* [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

* [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)

* [Runbook végrehajtási környezet](./automation-runbook-execution.md#runbook-execution-environment)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a privát hálózatkezeléshez a hibrid Runbook-feldolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, győződjön meg arról, hogy az azokat tartalmazó alhálózat hálózati biztonsági csoporttal (NSG) van engedélyezve, és konfigurálja a folyamat naplóit a naplók továbbításához a forgalmi naplózáshoz. NSG-naplókat is továbbíthat egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Habár a NSG-szabályok és a felhasználó által megadott útvonalak nem vonatkoznak a magánhálózati végpontra, a NSG és a kimenő kapcsolatok figyelési információi továbbra is támogatottak és használhatók.

* [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a privát hálózatkezeléshez a hibrid Runbook-feldolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, engedélyezze az elosztott szolgáltatásmegtagadási (DDoS) szabvány szerinti védelmet a hibrid Runbook-feldolgozókat üzemeltető virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony IP-címekkel. Konfigurálja Azure Firewall az egyes Virtual Network szegmenseken, és engedélyezze a fenyegetések felderítését, és konfigurálja a **riasztást, és tiltsa** le a kártékony hálózati forgalmat.

A Azure Security Center igény szerinti hálózati hozzáférésével korlátozhatja a Windows rendszerű virtuális gépeknek a jóváhagyott IP-címekre való kitettségét korlátozott ideig. Emellett használjon Azure Security Center adaptív hálózattal kapcsolatos javaslatokat a NSG konfigurációkhoz a portok és a forrás IP-címek korlátozásához a tényleges forgalom és a veszélyforrások felderítése alapján.

* [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

* [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

* [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

* [Azure Security Center adaptív hálózat megerősítésének ismertetése](../security-center/security-center-adaptive-network-hardening.md)

* [Azure Security Center az időponthoz tartozó hálózati Access Control ismertetése](../security-center/security-center-just-in-time.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: a Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a privát hálózatkezeléshez a hibrid Runbook-feldolgozók támogatásán túl, ez a vezérlő nem alkalmazható, ha hibrid feldolgozók nélkül használja a beépített szolgáltatást.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, akkor a NSG flow-naplókat rögzítheti egy Storage-fiókba, így a Runbook-feldolgozóként működő Azure-Virtual Machines flow-rekordokat hozhatja ki. A rendellenes tevékenység kivizsgálásakor engedélyezheti Network Watcher csomagok rögzítését, hogy a hálózati forgalom áttekinthető legyen a szokatlan és váratlan tevékenységekhez.

* [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a privát hálózatkezeléshez a hibrid Runbook-feldolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

Ha az Azure Virtual Machines szolgáltatásban üzemeltetett hibrid Runbook-feldolgozókat használ, a Network Watcher és a nyílt forráskódú AZONOSÍTÓk eszközei által biztosított csomagok rögzítését kombinálhatja a hálózati behatolás észlelése érdekében a feldolgozó gépekkel kapcsolatos fenyegetések széles köre érdekében. Emellett a megfelelő módon telepítheti Azure Firewallokat a Virtual Network szegmensekre, és a fenyegetések felderítése engedélyezve van, és úgy van beállítva, hogy a kártékony hálózati forgalom a "riasztás és megtagadás" legyen.

* [Hálózati behatolás-észlelés végrehajtása Network Watcher és nyílt forráskódú eszközökkel](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

* [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: Virtual Network szolgáltatás-címkék használatával definiálhatja a hálózati biztonsági csoportokhoz vagy az Azure-ban konfigurált Azure Firewallhoz szükséges hálózati hozzáférés-vezérlést, amelyhez az Automation-erőforrásokhoz való hozzáférésre van szükség. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például GuestAndHybridManagement) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

* [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Azure Automation által használt hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával.

Az Azure-tervrajzok segítségével a nagy léptékű Azure-környezeteket is leegyszerűsítheti a főbb környezeti összetevők (például az Azure Resources Manager-sablonok, az Azure RBAC-vezérlők és a házirendek) egyetlen tervezet-definícióban való csomagolásával. A tervrajzot új előfizetésekre alkalmazhatja, és az irányítás és felügyelet finomhangolását a verziószámozás segítségével végezheti el.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

* [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a NSG és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz használható címkék használata. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

* [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti az erőforrás-konfigurációkat, és felderítheti a hálózati erőforrások módosításait. Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus erőforrások változásai megváltoznak.

* [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Riasztások létrehozása a Azure Monitorban](../azure-monitor/platform/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrások időforrásait. Azonban lehetősége van a Windows rendszerű virtuális gépeken futó hibrid Runbook-feldolgozók időszinkronizálási beállításainak kezelésére.

* [Az Azure számítási erőforrások időszinkronizálásának konfigurálása](../virtual-machines/windows/time-sync.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplófájlok továbbítása Azure monitor naplókhoz Azure Automation erőforrások által generált biztonsági adatokat összesítve. A Azure Monitoron belül a naplók használatával kereshet és végezhet elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. A Azure Automation elküldheti a runbook feladatok állapotát, a feladatok adatfolyamait, az Automation állapotának konfigurációs adatait, az Update managementet, valamint a változások követését vagy leltározási naplóit a Log Analytics munkaterületre Ezek az információk láthatók a Azure Portal, Azure PowerShell és Azure Monitor naplók API-ból, amely lehetővé teszi az egyszerű vizsgálatok végrehajtását.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Azure Automation-feladat adatainak továbbítása az Azure Monitor-naplói felé](./automation-manage-send-joblogs-log-analytics.md)

* [A DSC integrálása Azure Monitor naplókkal](./automation-dsc-diagnostics.md)

* [A társított Log Analytics-munkaterület esetében támogatott régiók](./how-to/region-mappings.md)

* [Update Management naplók lekérdezése](./update-management/query-logs.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure monitor engedélyezése a naplózási és a tevékenységi naplók eléréséhez, beleértve az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célhelyek és más hasznos elemeket.

* [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: Ha Azure Automationt használ a több-bérlős runbook-feldolgozókkal, ez a vezérlő nem alkalmazható, és a platform kezeli az alapul szolgáló virtuális gépeket.

A hibrid Runbook Worker szolgáltatás használatakor a Azure Security Center biztonsági eseménynapló-figyelést biztosít a Windows rendszerű virtuális gépek számára. Ha a szervezete meg szeretné őrizni a biztonsági eseménynapló adatait, az egy adatgyűjtési szinten tárolható, amelyen Log Analytics lehet lekérdezni. Különböző szintek vannak: minimális, közös és mind, amelyek részletesen az alábbi hivatkozásra mutatnak.

* [Az adatgyűjtési rétegek konfigurálása Azure Security Centeron belül](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

* [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Az Automation-fiókok adatmegőrzési részletei](./automation-managing-data.md#data-retention)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre és a rendszeres eredményekre vonatkozó naplók elemzése és figyelése. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használjon Azure Monitor naplózási lekérdezéseket.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [A Azure Monitor naplózási lekérdezéseinek ismertetése](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a biztonsági naplókban és eseményekben található rendellenes tevékenységekkel kapcsolatos figyelési és riasztási Azure Security Center használata Azure monitor használatával.

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban.

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

* [Riasztás a Azure Monitor naplózási információi alapján](../azure-monitor/learn/tutorial-response.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: Ha több-bérlős runbook-feldolgozókkal Azure Automation használ, ez a vezérlő nem alkalmazható, és a platform kezeli az alapul szolgáló virtuális gépeket.

A hibrid Runbook-feldolgozói szolgáltatás használatakor azonban használhatja a Microsoft kártevő szoftvereket az Azure Cloud Services és a virtuális gépekhez. Konfigurálja úgy a virtuális gépeket, hogy naplózzák az eseményeket egy Azure Storage-fiókba. Konfiguráljon egy Log Analytics munkaterületet az események betöltéséhez a Storage-fiókokból, és szükség esetén hozzon létre riasztásokat. Kövesse az Azure Security Center: "számítási alkalmazások" című témakörben foglalt javaslatokat &amp; .

* [A Microsoft kártevő-elhárító szolgáltatásának konfigurálása Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

* [A virtuális gépek vendég szintű figyelésének engedélyezése](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: harmadik féltől származó megoldás megvalósítása az Azure Marketplace-ről a DNS-naplózási megoldáshoz, amelyet a szervezeteknek szüksége van.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: Ha Azure Automationt használ a több-bérlős runbook-feldolgozókkal, ez a vezérlő nem alkalmazható, és a platform kezeli az alapul szolgáló virtuális gépeket.

A hibrid Runbook Worker szolgáltatás használatakor azonban a Azure Security Center biztonsági eseménynapló-figyelést biztosít az Azure-beli virtuális gépek számára. Security Center kiépíti az Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépen, valamint az automatikus kiépítés engedélyezése esetén létrehozott újakat. Vagy manuálisan is telepítheti az ügynököt. Az ügynök engedélyezi a 4688-es folyamat-létrehozási eseményt és a 4688-es eseményen belüli parancssori mezőt. A virtuális gépen létrehozott új folyamatokat az eseménynapló rögzíti, és a Security Center észlelési szolgáltatásai figyelik.

* [Adatgyűjtés az Azure Security Centerben](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory beépített rendszergazdai szerepkörök használata, amelyek explicit módon hozzárendelhetők, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez. Ha Automation-fiók futtató fiókokat használ a runbookok, győződjön meg arról, hogy az ilyen egyszerű szolgáltatásokat a leltárban is nyomon követik, mivel ezek gyakran megemelt jogosultságokkal rendelkeznek. Törölje a nem használt futtató fiókokat a kitett támadási felület minimalizálásához.

* [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

* [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

* [Futtató fiók vagy klasszikus futtató fiók törlése](./delete-run-as-account.md)

* [Azure Automation futtató fiók kezelése](./manage-runas-account.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure Automation fiók nem rendelkezik az alapértelmezett jelszavak fogalmával. Az ügyfelek felelősek külső alkalmazások és piactér-szolgáltatásokért, amelyek a szolgáltatáson vagy a hibrid Runbook-feldolgozón futó alapértelmezett jelszavakat használhatják.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget. Ha Automation-fiók futtató fiókokat használ a runbookok, győződjön meg arról, hogy az ilyen egyszerű szolgáltatásokat a leltárban is nyomon követik, mivel ezek gyakran megemelt jogosultságokkal rendelkeznek. Ezek az identitások azokra a legalacsonyabb jogosultsági szintű engedélyekre terjednek ki, amelyekre szükségük van ahhoz, hogy a runbookok sikeresen elvégezze az automatikus folyamatot. Törölje a nem használt futtató fiókokat a kitett támadási felület minimalizálásához.

A Microsoft-szolgáltatásokhoz igénybe vehet egy igény szerinti vagy elég hozzáférésű hozzáférést Azure AD Privileged Identity Management Kiemelt szerepkörökkel, és Azure Resource Manager is.

* [További információ a Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

* [Futtató fiók vagy klasszikus futtató fiók törlése](./delete-run-as-account.md)

* [Azure Automation futtató fiók kezelése](./manage-runas-account.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: ha lehetséges, használja az egyszeri bejelentkezést Azure Active Directory helyett az önálló hitelesítő adatok konfigurálása a szolgáltatásban. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata.

* [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

* [Az Azure AD használata az Azure-beli hitelesítéshez](./automation-use-azure-ad.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure ad multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése.

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3,6: dedikált gépek használata az összes felügyeleti feladathoz

**Útmutató**: a többtényezős hitelesítéssel konfigurált és a Azure Automation fiók erőforrásainak éles környezetekben való bejelentkezni és konfigurálására szolgáló mancsok használata.

* [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: az Azure ad kockázati észlelések kihasználása a kockázatos felhasználói viselkedésre vonatkozó riasztások és jelentések megtekintéséhez. Szükség esetén az ügyfél Azure Security Center kockázatkezelési riasztásokat is továbbíthat Azure Monitor és az egyéni riasztások/értesítések műveleti csoportok használatával történő konfigurálásához.

* [Azure Security Center kockázati észlelések ismertetése (gyanús tevékenység)](../active-directory/identity-protection/overview-identity-protection.md)

* [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Műveleti csoportok konfigurálása egyéni riasztásokhoz és értesítésekhez](../azure-monitor/platform/action-groups.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: javasoljuk, hogy az elnevezett helyekkel rendelkező feltételes hozzáférés használatával engedélyezze a hozzáférést az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz.

* [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: az Azure ad használata központi hitelesítési és engedélyezési rendszerekként. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza. Hibrid Runbook-feldolgozók használata esetén a futtató fiókok helyett felügyelt identitásokat is használhat a zökkenőmentes biztonságos engedélyek engedélyezéséhez.

* [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

* [Runbook-hitelesítés használata felügyelt identitásokkal](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure ad olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. Ha Automation-fiók futtató fiókokat használ a runbookok, gondoskodjon arról, hogy ezeket a szolgáltatásokat a leltárban is nyomon kövessék, mivel ezek gyakran megemelt jogosultságokkal rendelkeznek. Törölje a nem használt futtató fiókokat a kitett támadási felület minimalizálásához.

* [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

* [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

* [Futtató fiók vagy klasszikus futtató fiók törlése](./delete-run-as-account.md)

* [Azure Automation futtató fiók kezelése](./manage-runas-account.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet az Azure ad bejelentkezési tevékenységeihez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

* [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure ad kockázati és identitás-védelmi funkcióinak használatával konfigurálhatja a hálózati erőforrás felhasználói identitásával kapcsolatos gyanús műveleteket. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

* [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

* [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: Azure Automation-fiókok esetében a Microsoft támogatási szolgálata a platform erőforrásaihoz tartozó metaadatokat egy másik eszköz használata nélkül is elérheti egy nyitott támogatási esetben.

Ha azonban az Azure Virtual Machines szolgáltatással támogatott hibrid Runbook-feldolgozókat használ, és egy külső félnek hozzá kell férnie az ügyféladatok eléréséhez (például egy támogatási kérelem során), az Azure Virtual Machines szolgáltatáshoz Ügyfélszéf (előzetes verzió) használatával tekintheti meg és hagyhatja jóvá vagy utasíthatja el az ügyféladatok hozzáférési kérelmeit.

* [A Ügyfélszéf ismertetése](../security/fundamentals/customer-lockbox-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure Automation-erőforrások nyomon követését.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. A környezetek elkülönítése külön Automation-fiók erőforrásainak használatával. Az olyan erőforrásokat, mint a hibrid Runbook-feldolgozók, a megfelelő címkével elválasztva, a hálózati biztonsági csoporton (NSG) és a Azure Firewallon belül biztonságossá kell tennie. A bizalmas adatokat tároló vagy feldolgozó virtuális gépek esetében a házirend és eljárás (ok) bevezetésével kikapcsolhatja azokat, ha nincsenek használatban.

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

* [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

* [Riasztások vagy riasztások konfigurálása és a Azure Firewall megtagadása](../firewall/threat-intel.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a hibrid Runbook-feldolgozói szolgáltatás használatakor az Azure Marketplace-ről származó külső megoldás kihasználható olyan hálózati környezetekben, amelyek figyelik a bizalmas információk jogosulatlan átvitelét, és blokkolja az adatátvitelt az adatbiztonsági szakemberek számára.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Győződjön meg arról, hogy az Azure-beli virtuális hálózatokban az Azure-erőforrásokhoz csatlakozó ügyfelek képesek legyenek egyeztetni a TLS 1,2-es vagy újabb verzióját. Azure Automation teljes mértékben támogatja és kikényszeríti a Transport Layer (TLS) 1,2 és az összes ügyfél-vagy újabb verziót minden külső HTPS-végponthoz (webhookok, DSC-csomópontok, hibrid runbook-feldolgozó).

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

* [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure Automation TLS 1,2 kényszerítés](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított bizalmas információk azonosítására egy külső féltől származó aktív felderítési eszköz használatával, beleértve a helyszíni vagy a távoli szolgáltatónál lévőket, valamint a szervezet bizalmas információinak leltározását.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozhatja a Azure Automation erőforrásokhoz való hozzáférést a beépített szerepkör-definíciók használatával, az Automation-erőforrásokhoz hozzáférő felhasználók számára a legkevésbé privilegizált vagy "elég" hozzáférési modellt követően. Hibrid Runbook-feldolgozók használata esetén a több-bérlős vagy hibrid Runbook-feldolgozók használata esetén a virtuális gépek felügyelt identitásait kell kihasználnia, ha a több-bérlős vagy a hibrid-feldolgozót egyaránt használja, ügyeljen arra, hogy megfelelően hatókörrel rendelkező Azure RBAC-engedélyeket alkalmazzon a Runbook

* [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

* [Runbook engedélyek a hibrid Runbook-feldolgozók számára](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [Szerepköri engedélyek és biztonság kezelése](./automation-role-based-access-control.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: a Azure Automation jelenleg nem teszi elérhetővé a több-bérlős runbook Worker virtuális gépeket, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, akkor egy harmadik féltől származó, gazdagépen alapuló adatvesztés-megelőzési megoldást kell használnia, amely kikényszeríti a hozzáférés-vezérlést az üzemeltetett hibrid Runbook Worker virtuális gépekhez.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az ügyfél által felügyelt kulcsok használata Azure Automationokkal. Azure Automation támogatja az ügyfél által felügyelt kulcsok használatát az összes felhasznált "biztonságos eszköz" titkosításához, például: hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók. A nem kívánt expozíció megelőzése érdekében a titkosított változókat az összes állandó változó keresési runbookok használja.

Hibrid Runbook-feldolgozók használata esetén a virtuális gépek virtuális lemezeinek tárolása a kiszolgálóoldali titkosítás vagy az Azure Disk Encryption (ADE) használatával történik. Az Azure Disk Encryption a Windows BitLocker szolgáltatásával titkosítja a felügyelt lemezeket az ügyfél által felügyelt kulcsokkal a vendég virtuális gépen. Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t, mivel lehetővé teszi a virtuális gépekhez tartozó operációsrendszer-típusok és-lemezképek használatát a Storage szolgáltatásban tárolt adattitkosítással.

* [Az Azure Managed Disks kiszolgálóoldali titkosítása](../virtual-machines/disk-encryption.md)

* [Azure Disk Encryption Windows rendszerű virtuális gépekhez](../virtual-machines/windows/disk-encryption-overview.md)

* [Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Felügyelt változók a Azure Automationban](./shared-resources/variables.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure Activity log használatával riasztásokat hozhat létre, amelyekkel a kritikus Azure-erőforrások, például a hálózati összetevők, a Azure Automation-fiókok és a runbookok változásaira kerül sor.

* [Hálózati biztonsági csoport diagnosztikai naplózása](../private-link/private-link-overview.md#logging-and-monitoring)

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/platform/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure Security Center ajánlásainak követése az Azure-erőforrások sebezhetőségi felmérésének végrehajtásával

* [Biztonsági javaslatok az Azure Security Centerben](../security-center/security-center-recommendations.md)

* [Security Center ajánlás referenciája](../security-center/recommendations-reference.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: a Azure Automation jelenleg nem teszi elérhetővé a több-bérlős runbook Worker virtuális gépeket, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, az Azure Update Management segítségével kezelheti a virtuális gépek frissítéseit és javításait. A Update Management a helyileg konfigurált frissítési tárházra támaszkodik a támogatott Windows-rendszerek javításához. Az olyan eszközök, mint a System Center Updates Publisher (a frissítések közzétevője) lehetővé teszik egyéni frissítések közzétételét Windows Server Update Services (WSUS) szolgáltatásban. Ez a forgatókönyv lehetővé teszi, hogy Update Management a harmadik féltől származó szoftverrel rendelkező frissítési adattárként Configuration Manager használó gépeket.

* [Update Management az Azure-ban](./update-management/overview.md)

* [A virtuális gépek frissítéseinek és javításának kezelése](./update-management/manage-updates-for-vm.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: a Azure Automation jelenleg nem teszi elérhetővé a több-bérlős runbook Worker virtuális gépeket, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, az Azure Update Management segítségével kezelheti a virtuális gépek frissítéseit és javításait. A Update Management a helyileg konfigurált frissítési tárházra támaszkodik a támogatott Windows-rendszerek javításához. Az olyan eszközök, mint a System Center Updates Publisher (a frissítések közzétevője) lehetővé teszik egyéni frissítések közzétételét Windows Server Update Services (WSUS) szolgáltatásban. Ez a forgatókönyv lehetővé teszi, hogy Update Management a harmadik féltől származó szoftverrel rendelkező frissítési adattárként Configuration Manager használó gépeket.

* [Update Management megoldás az Azure-ban](./update-management/overview.md)

* [Azure-beli virtuális gépek frissítéseinek és javításának kezelése](./update-management/manage-updates-for-vm.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a vizsgálati eredmények konzisztens időközönkénti exportálása és az eredmények összehasonlítása annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. Ha Azure Security Center által javasolt sebezhetőségi kezelési javaslatot használ, az ügyfél a kiválasztott megoldás portálján megtekintheti a korábbi vizsgálati adataikat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata a felderített biztonsági rések szervizelésének rangsorolása érdekében.

* [A Azure Security Center biztonsági pontszámának megismerése](../security-center/secure-score-security-controls.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával kérdezheti le és derítheti fel az előfizetésekben található összes Azure Automation erőforrást. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

* [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

* [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

* [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ha szükséges, a Azure Automation erőforrások rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen. Törölje a nem használt futtató fiókokat a kitett támadási felület minimalizálásához.

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Futtató fiók vagy klasszikus futtató fiók törlése](./delete-run-as-account.md)

* [Azure Automation futtató fiók kezelése](./manage-runas-account.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: létre kell hoznia egy leltárt a jóváhagyott Azure-erőforrásokról és jóváhagyott szoftverekről a számítási erőforrásokhoz a szervezeti igényeknek megfelelően.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetéseken belüli erőforrásokat. Ez magas biztonsági alapú környezetekben, például a Storage-fiókokkal is segít.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

* [Azure Policy minta beépített Azure Automation](./policy-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: az Azure Automation ajánlat jelenleg nem teszi elérhetővé a több-bérlős runbook Worker virtuális gépeket, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha hibrid feldolgozók nélkül használja a beépített szolgáltatást. Azonban lehetséges a PowerShell, illetve a runbookok által a portálon vagy a parancsmagokon keresztül elérhető Python-modulok telepítése, eltávolítása és kezelése. A nem jóváhagyott vagy régi modult el kell távolítani vagy frissíteni kell a runbookok.

Ha az Azure Virtual Machines által támogatott hibrid Runbook-feldolgozókat használ, a Azure Automation teljes körű ellenőrzést biztosít a munkaterhelések és erőforrások üzembe helyezése, üzemeltetése és leszerelése során. Az Azure-beli virtuális gépek Leltározásával automatizálhatja a Virtual Machines összes szoftverével kapcsolatos információk gyűjtését. A szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A telepítés dátumának és egyéb információinak eléréséhez az ügyfélnek a vendég szintű diagnosztika engedélyezéséhez és a Windows-eseménynaplók Log Analytics munkaterületre való bekapcsolásához van szükség.

* [Bevezetés az Azure Automationbe](./automation-intro.md)

* [Az Azure-beli virtuális gépek leltározásának engedélyezése](./automation-tutorial-installed-software.md)

* [Modul kezelése Azure Automation](./shared-resources/modules.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: az ügyfél meggátolhatja az erőforrások létrehozását és használatát Azure Policy az ügyfél vállalati iránymutatásainak megfelelően. A jogosulatlan erőforrások eltávolításához saját folyamatot is alkalmazhat. A Azure Automation-ajánlaton belül lehetőség van a PowerShell, illetve a runbookok által a portálon vagy a parancsmagokon keresztül elérhető Python-modulok telepítésére, eltávolítására és kezelésére. A nem jóváhagyott vagy régi modult el kell távolítani vagy frissíteni kell a runbookok.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Modul kezelése Azure Automation](./shared-resources/modules.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: a hibrid Runbook Worker szolgáltatás használatakor Azure Security Center adaptív alkalmazás-vezérlőelemekkel biztosíthatja, hogy csak a hitelesített szoftverek legyenek végrehajtva, és az összes jogosulatlan szoftver le legyen tiltva az Azure Virtual machines.

* [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: Ha a hibrid Runbook Worker szolgáltatást használja, használhatja a Azure Security Center adaptív alkalmazás-vezérlők funkciót a hibrid feldolgozói virtuális gépekkel.

Az adaptív alkalmazás-vezérlés intelligens, automatizált, teljes körű megoldás a Azure Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-beli és nem Azure-beli gépeken (Windows és Linux). Harmadik féltől származó megoldás implementálása, ha ez nem felel meg a szervezet követelményének.

* [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférési szabályzatok használatával korlátozhatja a felhasználók azon képességét, hogy a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításának a nem biztonságos vagy nem jóváhagyott helyekről vagy eszközökről történő konfigurálásával korlátozza a Azure Resource Manager interakcióját.

* [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: a hibrid Runbook-feldolgozói szolgáltatás használatakor, valamint a parancsfájlok típusától függően használhat operációs rendszer-specifikus konfigurációkat vagy harmadik féltől származó erőforrásokat, hogy korlátozza a felhasználók számára az Azure számítási erőforrásokon belüli parancsfájlok végrehajtását. Emellett kihasználhatja Azure Security Center adaptív alkalmazás vezérlőelemeit is, hogy csak a hitelesített szoftverek fussanak, és az összes jogosulatlan szoftver le legyen tiltva az Azure Virtual Machines.

* [A PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](/powershell/module/microsoft.powershell.security/set-executionpolicy)

* [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az Azure-környezetben üzembe helyezett magas kockázatú alkalmazások elkülönítése különálló hálózati és erőforrás-tárolók használatával, például virtuális hálózatokkal, alhálózattal, előfizetésekkel és felügyeleti csoportokkal, a Azure Firewall, a webalkalmazási TŰZFAL (WAF) vagy a hálózati biztonsági csoport (NSG) segítségével megfelelően védhetők.

* [Virtuális hálózatok és virtuális gépek az Azure-ban](../virtual-machines/network-overview.md)

* [Azure Firewall áttekintése](../firewall/overview.md)

* [Az Azure webalkalmazási tűzfal áttekintése](../web-application-firewall/overview.md)

* [Hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md)

* [Az Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)

* [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)

* [Útmutató az előfizetéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy-aliasok használata egyéni szabályzatok létrehozásához a Azure Automation és a kapcsolódó erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. A beépített Azure Policy-definíciókat is használhatja.

Emellett Azure Resource Manager lehetősége van a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni, hogy a konfigurációk megfeleljenek/túllépik a szervezete biztonsági követelményeit.

Az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően Azure Security Center javaslatokat is alkalmazhat.

* [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

* [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy minta beépített Azure Automation](./policy-reference.md)

* [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

* [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: a Azure Automation jelenleg nem teszi elérhetővé a több-bérlős runbook Worker virtuális gépeket vagy operációs rendszert. Ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

A hibrid Runbook-feldolgozói szolgáltatás használatakor Azure Security Center javaslattal [javítsa ki a biztonsági réseket a Virtual Machines] a biztonsági konfigurációk karbantartásához a virtuális gépeken.

* [Azure Security Center javaslatok figyelése](../security-center/security-center-recommendations.md)

* [Azure Security Center javaslatok szervizelése](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: a Azure Automationhoz társított Azure-erőforrások biztonságos konfigurálásához Azure Resource Manager sablonok és Azure Policy használatával. Azure Resource Manager a sablonok az Azure-erőforrások üzembe helyezéséhez használt JSON-alapú fájlok, és az egyéni sablonokat biztonságosan kell tárolni és karbantartani egy adattárban. A verziókövetés integrációs funkciójával naprakészen tarthatja a runbookok az Automation-fiókban a verziókövetés adattárában található parancsfájlok használatával. Az Azure-erőforrások biztonságos beállításainak érvénybe léptetéséhez használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

* [Verziókövetés-integrálás használata](./source-control-integration.md)

* [Azure Resource Manager sablonok létrehozásával kapcsolatos információk](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

* [Automation-fiók üzembe helyezése Azure Resource Manager sablon használatával](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Azure Policy minta beépített Azure Automation](./policy-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: a Azure Automation jelenleg nem teszi elérhetővé a több-bérlős runbook Worker virtuális gépeket vagy operációs rendszert, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

A hibrid Runbook-feldolgozói szolgáltatás használatakor több lehetőség áll rendelkezésre az Azure-beli virtuális gépek biztonságos konfigurációjának karbantartására az üzembe helyezéshez:

- Azure Resource Manager-sablonok: ezek olyan JSON-alapú fájlok, amelyek a virtuális gép Azure Portal való üzembe helyezéséhez használhatók, és az egyéni sablont is karban kell tartani. A Microsoft végzi el a karbantartást az alapsablonokon.
- Egyéni virtuális merevlemez (VHD): bizonyos esetekben előfordulhat, hogy olyan egyéni VHD-fájlokat kell használnia, mint például a más módon nem felügyelhető összetett környezetek kezelése.
- Azure Automation állapot konfigurálása: miután telepítette az alaprendszert, ez a beállítás részletesebben szabályozható a beállításokban, és az Automation-keretrendszer segítségével kényszeríthető ki.

A legtöbb esetben a Azure Automation állapot-konfigurációval kombinált Microsoft Base VM-sablonok segíthetnek a biztonsági követelmények teljesítésében és fenntartásában.

* [A virtuálisgép-sablon letöltésével kapcsolatos információk](/previous-versions/azure/virtual-machines/windows/download-template)

* [Az ARM-sablonok létrehozásával kapcsolatos információk](../virtual-machines/windows/ps-template.md)

* [Egyéni virtuális merevlemez feltöltése az Azure-ba](/azure-stack/operator/azure-stack-add-vm-image)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, a Azure Resource Manager sablonokat és a kívánt állapotú konfigurációs parancsfájlokat. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy a Azure Active Directoryban definiált csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van. A verziókövetés integrációs funkciójával naprakészen tarthatja a runbookok az Automation-fiókban a verziókövetés adattárában található parancsfájlok használatával.

* [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Verziókövetés-integrálás használata](./source-control-integration.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: a Azure Automation jelenleg nem teszi elérhetővé a több-bérlős runbook Worker virtuális gépeket vagy operációs rendszert, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

A hibrid Runbook Worker szolgáltatás használatakor győződjön meg arról, hogy megfelelően korlátozza a hozzáférést a Storage-fiókban található egyéni operációsrendszer-rendszerképhez, így csak a jogosult felhasználók férhetnek hozzá a képhez.

* [Az Azure RBAC ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Az Azure RBAC konfigurálása](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Az Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

* [Azure Policy minta beépített Azure Automation](./policy-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: a Azure Automation jelenleg nem teszi elérhetővé a több-bérlős runbook Worker virtuális gépeket vagy operációs rendszert, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

A hibrid Runbook-feldolgozói szolgáltatás használatakor használja Azure Automation állapot konfigurációját a Runbook-munkavégzők számára, amely a kívánt állapot-konfigurációs (DSC) csomópontok számára a konfiguráció-felügyeleti szolgáltatás bármely Felhőbeli vagy helyszíni adatközpontban. Lehetővé teszi, hogy gyorsan és könnyen, egy központi, biztonságos helyen lehessen méretezni a több ezer gépen. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják.

* [Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](./automation-dsc-onboarding.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: az Azure-erőforrások konfigurációjának riasztására és naplózására szolgáló Azure Policy használata, a szabályzat segítségével azonosíthatja a privát végponttal nem konfigurált erőforrásokat.

A hibrid Runbook Worker szolgáltatás használatakor a Azure Security Center kihasználhatja az Azure-beli virtuális gépek alapkonfigurációjának vizsgálatát. Az automatikus konfiguráció további módszerei a Azure Automation állapot konfigurációját is tartalmazzák.

* [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Azure Automation állapot konfigurációjának első lépései](./automation-dsc-getting-started.md)

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy minta beépített Azure Automation](./policy-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: az Azure Automation ajánlat jelenleg nem teszi elérhetővé az alapul szolgáló több-bérlős runbook-feldolgozó virtuális gépeket vagy operációs rendszert, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha hibrid feldolgozók nélkül használja a beépített szolgáltatást.

A hibrid Runbook-feldolgozói szolgáltatás használatakor használja Azure Automation állapot konfigurációját a Runbook-feldolgozók számára, amely egy, a felhőben vagy a helyszíni adatközpontban a kívánt állapot-konfigurációs (DSC) csomópontok számára a Configuration Management szolgáltatás. Lehetővé teszi, hogy gyorsan és könnyen, egy központi, biztonságos helyen lehessen méretezni a több ezer gépen. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják.

* [Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](./automation-dsc-onboarding.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében.

* [Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Runbook-hitelesítés használata felügyelt identitásokkal](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [Key Vault létrehozása](../key-vault/general/quick-create-portal.md)

* [Hitelesítés Key Vault](../key-vault/general/authentication.md)

* [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure ad-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

* [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

* [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: az Azure Automation ajánlat jelenleg nem teszi elérhetővé az alapul szolgáló több-bérlős runbook-feldolgozó virtuális gépeket vagy operációs rendszert, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

A hibrid Runbook-feldolgozói szolgáltatás használatakor az Azure Windows rendszerű virtuális gépekhez készült Microsoft kártevő-elhárító használatával folyamatosan figyelheti és védheti a Runbook Worker-erőforrásait.

* [A Microsoft kártevő-elhárító szolgáltatásának konfigurálása Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: nem alkalmazható; Azure Automation, mert a szolgáltatás nem tárol fájlokat. A Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például Azure Automation), de nem fut a tartalomon.

* [Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: a Azure Automation jelenleg nem teszi elérhetővé a több-bérlős runbook Worker virtuális gépeket vagy operációs rendszert, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha a beépített szolgáltatást használja hibrid Runbook-feldolgozók nélkül.

A hibrid Runbook Worker szolgáltatás használatakor az Azure-hoz készült Microsoft antimalware használatával automatikusan telepítheti a legújabb aláírás-, platform-és motor-frissítéseket a Runbook-feldolgozóra. Kövesse az Azure Security Center: "számítási alkalmazások" című témakörben található javaslatokat &amp; , hogy az összes végpont naprakész legyen a legújabb aláírásokkal. A Windows operációs rendszer további biztonsággal biztosítható, hogy a Microsoft Defender komplex veszélyforrások elleni védelmi szolgáltatásával a Azure Security Center-val integrálható vírus-vagy kártevő-alapú támadások kockázatát korlátozzák.

* [A Microsoft antimalware telepítése Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure Resource Manager használatával Azure Automation-fiókokat és kapcsolódó erőforrásokat telepíthet. A Azure Resource Manager lehetővé teszi olyan sablonok exportálását, amelyek biztonsági mentésként használhatók Azure Automation fiókok és kapcsolódó erőforrások visszaállításához. A Azure Automation használatával rendszeresen meghívhatja a Azure Resource Manager sablon exportálási API-ját.

A verziókövetés integrációs funkciójával naprakészen tarthatja a runbookok az Automation-fiókban a verziókövetés adattárában található parancsfájlok használatával.

* [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

* [Azure Resource Manager sablon referenciája Azure Automation erőforrásokhoz](/azure/templates/microsoft.automation/allversions)

* [Automation-fiók létrehozása Azure Resource Manager sablon használatával](./quickstart-create-automation-account-template.md)

* [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

* [Erőforráscsoportok – sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

* [Bevezetés a Azure Automationba](./automation-intro.md)

* [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

* [Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Verziókövetés-integrálás használata](./source-control-integration.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure Resource Manager használatával Azure Automation-fiókokat és kapcsolódó erőforrásokat telepíthet. A Azure Resource Manager lehetővé teszi olyan sablonok exportálását, amelyek biztonsági mentésként használhatók Azure Automation fiókok és kapcsolódó erőforrások visszaállításához. A Azure Automation használatával rendszeresen meghívhatja a Azure Resource Manager sablon exportálási API-ját. Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül. A runbookok a Azure Portal vagy a PowerShell használatával is exportálhatja parancsfájl-fájlokba.

* [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

* [Azure Resource Manager sablon referenciája Azure Automation erőforrásokhoz](/azure/templates/microsoft.automation/allversions)

* [Automation-fiók létrehozása Azure Resource Manager sablon használatával](./quickstart-create-automation-account-template.md)

* [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

* [Erőforráscsoportok – sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

* [Bevezetés a Azure Automationba](./automation-intro.md)

* [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

* [Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Azure-beli adatbiztonsági mentés Automation-fiókokhoz](./automation-managing-data.md#data-backup)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az Azure Resource Manager-sablonok rendszeres időközönkénti üzembe helyezésének lehetősége egy elkülönített előfizetésre, ha szükséges. Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását.

* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

* [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

* [Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például Azure Resource Manager sablonokat. Az Azure DevOps felügyelt erőforrásainak védelme érdekében engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználóknak, beépített biztonsági csoportoknak, illetve a Azure Active Directoryban definiált, az Azure DevOps-be integrált vagy Active Directory, ha a TFS integrált.

A verziókövetés integrációs funkciójával naprakészen tarthatja a runbookok az Automation-fiókban a verziókövetés adattárában található parancsfájlok használatával.

* [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Verziókövetés-integrálás használata](./source-control-integration.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

* [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

* [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

* [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

* [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

* [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

* [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

* [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

* [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

* [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)