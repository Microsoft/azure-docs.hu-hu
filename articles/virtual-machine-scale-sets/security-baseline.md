---
title: Virtual Machine Scale Sets Azure biztonsági alapterve
description: A Virtual Machine Scale Sets biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2d902bbdc03596fe246fc36813895e72c53da05a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571405"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets Azure biztonsági alapterve

Az Virtual Machine Scale Sets Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: Ha létrehoz egy Azure-beli virtuális GÉPET (VM), létre kell hoznia egy virtuális hálózatot, vagy egy meglévő virtuális hálózatot kell használnia, és a virtuális gépet egy alhálózattal kell konfigurálnia. Győződjön meg arról, hogy az összes telepített alhálózat rendelkezik olyan hálózati biztonsági csoporttal, amely az alkalmazások megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel rendelkezik.

Ha a központosított tűzfalhoz adott használati esettel rendelkezik, Azure Firewall is felhasználhatja ezeket a követelményeket.

* [Azure-beli virtuálisgép-méretezési csoportok hálózatkezelése](./virtual-machine-scale-sets-networking.md)

* [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

* [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center segítségével azonosíthatja és követheti a hálózatvédelemre vonatkozó ajánlásokat az Azure-beli virtuális gépek (VM) erőforrásainak biztonságossá tételéhez az Azure-ban. Engedélyezze a NSG flow-naplókat, és küldje el a naplókat egy Storage-fiókba a szokatlan tevékenységű virtuális gépek forgalmának naplózásához.

* [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: Ha a virtuálisgép-méretezési CSOPORTOT (VMSS) használja a webalkalmazások üzemeltetéséhez, használjon egy hálózati biztonsági CSOPORTOT (NSG) a VMSS alhálózaton annak korlátozására, hogy milyen hálózati forgalom, portok és protokollok kommunikáljanak a kommunikációban. Ha úgy konfigurálja a NSG, hogy csak a szükséges forgalmat engedélyezze az alkalmazás számára, kövesse a legkevésbé Kiemelt hálózati megközelítést.

Az Azure webalkalmazási tűzfal (WAF) a kritikus webalkalmazások előtt is üzembe helyezhető a bejövő forgalom további ellenőrzéséhez. Diagnosztikai beállítás engedélyezése a WAF és a naplók betöltéséhez egy Storage-fiókba, az Event hub-ba vagy a Log Analytics-munkaterületre.

* [Azure-beli virtuálisgép-méretezési csoportok hálózatkezelése](./virtual-machine-scale-sets-networking.md)

* [Application Gateway létrehozása webalkalmazási tűzfallal a Azure Portal használatával](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az elosztott szolgáltatásmegtagadási (DDoS) standard szintű védelem engedélyezése a virtuális hálózatokon a DDOS-támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetési intelligencia használatával figyelheti az ismert kártékony IP-címekkel folytatott kommunikációt. Konfigurálja Azure Firewall az egyes Virtual Network szegmenseken, és engedélyezze a fenyegetések felderítését, és állítsa be a "riasztás és megtagadás" beállítást a kártékony hálózati forgalom számára.

A Azure Security Center igény szerinti hálózati hozzáférésével korlátozhatja a jóváhagyott IP-címekre való Windows Virtual Machines kitettségét korlátozott időtartamra. Emellett Azure Security Center adaptív hálózati korlátozással olyan NSG-konfigurációkat ajánlunk, amelyek a portok és a forrás IP-címeket a tényleges forgalom és a veszélyforrások felderítése alapján korlátozzák.

* [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

* [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

* [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

* [Azure Security Center adaptív hálózat megerősítésének ismertetése](../security-center/security-center-adaptive-network-hardening.md)

* [Azure Security Center az időponthoz tartozó hálózati Access Control ismertetése](../security-center/security-center-just-in-time.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: rögzíthet NSG-naplókat egy Storage-fiókba, hogy adatforgalmi rekordokat lehessen készíteni az Azure-Virtual Machines számára. A rendellenes tevékenység kivizsgálásakor engedélyezheti Network Watcher csomagok rögzítését, hogy a hálózati forgalom áttekinthető legyen a szokatlan és váratlan tevékenységekhez.

* [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Network Watcher és egy nyílt forráskódú azonosító eszköz által biztosított csomagok rögzítésének kombinálásával a fenyegetések széles köre miatt hálózati behatolás-észlelést is végezhet. Emellett a megfelelő módon telepítheti Azure Firewall a Virtual Network szegmensekre, és a fenyegetések felderítése engedélyezve van, és úgy van beállítva, hogy a kártékony hálózati forgalom a "riasztás és megtagadás" legyen.

* [Hálózati behatolás-észlelés végrehajtása Network Watcher és nyílt forráskódú eszközökkel](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

* [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: Ha a virtuálisgép-méretezési csoport (VMSS) használatával üzemelteti a webalkalmazásokat, az Azure Application Gateway a megbízható tanúsítványok számára engedélyezett https/SSL-alapú webalkalmazásokhoz is üzembe helyezhető. Az Azure Application Gateway használatával a figyelőket a portokhoz, a szabályok létrehozásához, valamint a háttérbeli készletekhez, például a VMSS-hoz, és erőforrásokat adhat hozzá adott erőforrásokhoz.

* [Application Gateway üzembe helyezése](../application-gateway/quick-create-portal.md)

* [A Application Gateway konfigurálása a HTTPS használatára](../application-gateway/create-ssl-portal.md)

* [Application Gateway-re hivatkozó méretezési csoport létrehozása](./virtual-machine-scale-sets-networking.md#create-a-scale-set-that-references-an-application-gateway)

* [A 7. rétegbeli terheléselosztás és az Azure-webalkalmazás-átjárók ismertetése](../application-gateway/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: Virtual Network szolgáltatás-címkék használata hálózati biztonsági csoportokon vagy az Azure-beli virtuális gépekhez konfigurált Azure Firewall hálózati hozzáférés-vezérlés definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

* [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása az Azure Virtual Machine Scale sets Azure Policy használatával. Az Azure-tervrajzok segítségével leegyszerűsítheti a nagyméretű Azure-beli virtuális gépek üzembe helyezését a főbb környezeti összetevők, például a Azure Resource Manager sablonok, szerepkör-hozzárendelések és Azure Policy-hozzárendelések egyetlen tervrajz-definícióban való kicsomagolásával. A tervezetet alkalmazhatja az előfizetésekre, és engedélyezheti az erőforrás-kezelést a tervrajzok verziószámozásával.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Tudnivalók a virtuálisgép-méretezési csoport sablonjairól](./virtual-machine-scale-sets-mvss-start.md)

* [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

* [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használhat címkéket a hálózati biztonsági csoportokhoz (NSG) és a Windows rendszerű virtuális gépekhez konfigurált hálózati biztonsággal és forgalommal kapcsolatos egyéb erőforrásokhoz is. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot a hálózatra irányuló vagy onnan érkező forgalmat engedélyező szabályokhoz.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

* [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használatával figyelheti az Azure virtuálisgép-méretezési csoporttal kapcsolatos hálózati erőforrás-konfigurációk változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati beállításokat vagy erőforrásokat módosítják.

Használja a Azure Policy a virtuálisgép-méretezési csoporttal kapcsolatos hálózati erőforrás érvényesítéséhez (és/vagy szervizeléséhez).

* [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrások időforrásait, de lehetősége van a Virtual Machines időszinkronizálási beállításainak kezelésére.

* [Az időszinkronizálás konfigurálása az Azure Windows számítási erőforrásaihoz](../virtual-machines/windows/time-sync.md)

* [Az időszinkronizálás konfigurálása az Azure Linux számítási erőforrásaihoz](../virtual-machines/linux/time-sync.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a Tevékenységnaplók használhatók a virtuálisgép-méretezési csoport erőforrásain végrehajtott műveletek és műveletek naplózására. A tevékenység naplója az erőforrások összes írási műveletét (PUT, POST, DELETE) tartalmazza az olvasási műveletek (GET) kivételével. A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

Az Azure-tevékenység naplóiból vagy a virtuális gépek erőforrásaiból az Azure Sentinelbe vagy egy harmadik féltől származó, a központi biztonsági naplók felügyeletéhez létrehozott adatnaplót engedélyezheti és elvégezheti.

A Azure Security Center használatával biztosíthatja az Azure Virtual Machines biztonsági eseménynaplóinak figyelését. A biztonsági eseménynapló által generált adatmennyiség miatt a szolgáltatás alapértelmezés szerint nem tárolja azt.

Ha a szervezete szeretné megőrizni a biztonsági eseménynapló adatait a virtuális gépről, akkor a Log Analytics munkaterületen belül tárolhatja a Azure Security Centeron belül konfigurált kívánt adatgyűjtési szinten.

* [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Adatgyűjtés az Azure Security Centerben](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Tevékenységnaplók használhatók a virtuálisgép-méretezési csoport erőforrásain végrehajtott műveletek és műveletek naplózására. A tevékenység naplója az erőforrások összes írási műveletét (PUT, POST, DELETE) tartalmazza az olvasási műveletek (GET) kivételével. A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

A vendég operációs rendszer diagnosztikai adatai gyűjtésének engedélyezése a Virtual Machines (VM) diagnosztikai bővítményének üzembe helyezésével. A diagnosztika bővítmény használatával diagnosztikai adatokat gyűjthet, például az alkalmazás naplóit vagy a teljesítményszámlálók egy Azure-beli virtuális gépről.

Az Azure virtuálisgép-méretezési csoport által támogatott alkalmazások és szolgáltatások speciális láthatósága érdekében a Azure Monitor for VMs és az Application betekintést is engedélyezheti. A Application Insights segítségével figyelheti az alkalmazást, és rögzítheti a telemetria, például HTTP-kérelmeket, kivételeket stb., így a virtuális gépek és az alkalmazás közötti problémák is összekapcsolhatók.

* [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

* [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)

* [Az Application Insights áttekintése](../azure-monitor/app/app-insights-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: a Azure Security Center használatával biztosíthatja az Azure Virtual Machines biztonsági eseménynaplóinak figyelését. A biztonsági eseménynapló által generált adatmennyiség miatt a szolgáltatás alapértelmezés szerint nem tárolja azt.

Ha a szervezete szeretné megőrizni a biztonsági eseménynapló adatait a virtuális gépről, akkor a Log Analytics munkaterületen belül tárolhatja a Azure Security Centeron belül konfigurált kívánt adatgyűjtési szinten.

* [Adatgyűjtés az Azure Security Centerben](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: gondoskodjon arról, hogy a virtuális gépek naplófájljainak tárolására használt összes Storage-fiók vagy log Analytics-munkaterület a szervezet megfelelőségi szabályainak megfelelően állítsa be a napló megőrzési időtartamát.

* [Virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)

* [Log Analytics munkaterület megőrzési időtartamának konfigurálása](../azure-monitor/logs/manage-cost-storage.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre és a rendszeres eredményekre vonatkozó naplók elemzése és figyelése. Az Azure Monitor használatával tekintheti át a naplókat, és lekérdezéseket hajthat végre a naplózási adatokon.

Azt is megteheti, hogy az Azure Sentinel vagy egy harmadik féltől származó SIEM-t is engedélyez és biztosít a naplók monitorozásához és áttekintéséhez.

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [Log Analytics munkaterület ismertetése](../azure-monitor/logs/log-analytics-tutorial.md)

* [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a log Analytics munkaterülettel konfigurált Azure Security Center használata az Azure-Virtual Machines biztonsági naplóiban és eseményeiben észlelt rendellenes tevékenységek figyelésére és riasztására.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-hez kapcsolódó riasztások beállítását a rendellenes tevékenységekhez.

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

* [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: használhatja az Azure-hoz készült Microsoft kártevő szoftvereket Cloud Services és Virtual Machines és konfigurálhatja a Windows rendszerű virtuális gépeket az események Azure Storage-fiókba való naplózásához. Konfiguráljon egy Log Analytics munkaterületet az események betöltéséhez a Storage-fiókokból, és szükség esetén hozzon létre riasztásokat. Kövesse az Azure Security Center: "számítási alkalmazások" című témakörben foglalt javaslatokat &amp; . Linuxos virtuális gépek esetén szüksége lesz egy külső gyártótól származó eszközre a kártevő szoftverek elleni sebezhetőség észleléséhez.

* [A Microsoft kártevő-elhárító szolgáltatásának konfigurálása Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

* [A Virtual Machines vendég szintű figyelésének engedélyezése](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

* [Útmutató Linux-kiszolgálók Azure Security Centerbe való bevezetéséhez](../security-center/quickstart-onboard-machines.md)

* [A következő hivatkozásra kattintva megtekintheti a Microsoft ajánlott biztonsági irányelveit, amelyek feltételként szolgálhatnak a biztonsági rések szoftver kiválasztásához.](../virtual-machines/security-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: harmadik féltől származó megoldás megvalósítása az Azure Marketplace-ről a DNS-naplózási megoldáshoz, amelyet a szervezeteknek szüksége van.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: a Azure Security Center biztonsági eseménynapló-figyelést biztosít az Azure Virtual Machines (VM) számára. Security Center kiépíti a Microsoft monitoring agentet az összes támogatott Azure-beli virtuális gépre, valamint az automatikus kiépítés engedélyezése esetén létrehozott újakra, vagy manuálisan is telepítheti az ügynököt. Az ügynök engedélyezi a 4688-es folyamat-létrehozási eseményt és a 4688-es eseményen belüli parancssori mezőt. A virtuális gépen létrehozott új folyamatokat az eseménynapló rögzíti, és a Security Center észlelési szolgáltatásai figyelik.

Linux rendszerű virtuális gépek esetén a konzol naplózását manuálisan is konfigurálhatja a csomópontok alapján, és a syslogs használatával tárolhatja az adattárakat. Emellett a Azure Monitor Log Analytics munkaterületén áttekintheti a naplókat, és lekérdezéseket hajthat végre a syslog-adatokon az Azure Virtual Machines szolgáltatásból.

* [Adatgyűjtés az Azure Security Centerben](../security-center/security-center-enable-data-collection.md#data-collection-tier)

* [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

* [Rendszernapló-adatforrások az Azure Monitorban](../azure-monitor/agents/data-sources-syslog.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory a felhasználói hozzáférés felügyeletének ajánlott módszere, az Azure Virtual Machines helyi fiókokkal rendelkezhetnek. A helyi és a tartományi fiókokat egyaránt felül kell vizsgálni és felügyelni kell, általában minimális helyigénysel. Emellett az Azure Privileged Identity Management is kihasználhatja a virtuális gépek erőforrásainak eléréséhez használt rendszergazdai fiókokhoz.

* [A helyi fiókok információi a következő címen érhetők el:](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

* [Információk a Privileged Identity Managerről](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az Azure virtuálisgép-méretezési csoport és a Azure Active Directory nem rendelkezik az alapértelmezett jelszavakkal. A harmadik féltől származó alkalmazásokért és Marketplace-szolgáltatásért felelős ügyfél, amely az alapértelmezett jelszavakat használja.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos működési eljárások létrehozása a virtuális gépekhez hozzáféréssel rendelkező dedikált rendszergazdai fiókok használatával. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget. Az Azure-beli virtuális gépek erőforrásainak eléréséhez használt rendszergazdai fiókok az Azure Privileged Identity Management (PIM) segítségével is kezelhetők. Az Azure Privileged Identity Management számos lehetőséget kínál, például igény szerinti jogosultságszint-emelést, Multi-Factor Authentication megkövetelése a szerepkör és a delegálási beállítások megadásához, hogy az engedélyek csak meghatározott időkeretek számára legyenek elérhetők, és a jóváhagyó megkövetelése.

* [Azure Security Center identitás és hozzáférés ismertetése](../security-center/security-center-identity-access.md)

* [Információk a Privileged Identity Managerről](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: ha lehetséges, használja az egyszeri bejelentkezést Azure Active Directory helyett az önálló hitelesítő adatok konfigurálása a szolgáltatásban. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata.

* [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure ad MFA engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése.

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: az Azure-erőforrások bejelentkezésére és konfigurálására szolgáló, az MFA használatára konfigurált, a mancsok (emelt szintű hozzáférési munkaállomások) használata.

* [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure ad PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg. Igény szerint az ügyfél Azure Security Center kockázatkezelési riasztásokat is betöltheti a Azure Monitorba, és egyéni riasztásokat/értesítéseket állíthat be a műveleti csoportok használatával.

* [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure Security Center kockázati észlelések ismertetése (gyanús tevékenység)](../active-directory/identity-protection/overview-identity-protection.md)

* [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Műveleti csoportok konfigurálása egyéni riasztásokhoz és értesítésekhez](../azure-monitor/alerts/action-groups.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: Azure Active Directory feltételes hozzáférési szabályzatok és elnevezett helyszínek használatával engedélyezheti a hozzáférést az IP-címtartományok vagy országok/régiók csak bizonyos logikai csoportjaiból.

* [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza. A felügyelt identitások használatával bármely olyan szolgáltatás hitelesíthető, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vaultt is, a kódban szereplő hitelesítő adatok nélkül. A virtuális gépen futó kód a felügyelt identitás használatával kérhet hozzáférési jogkivonatokat az Azure AD-hitelesítést támogató szolgáltatások számára.

* [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

* [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](../active-directory/managed-identities-azure-resources/overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure ad olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett a Azure Active Directory Identity Access Reviews használatával hatékonyan kezelhet csoporttagság, hozzáférés a vállalati alkalmazásokhoz és a szerepkör-hozzárendelésekhez. A felhasználó hozzáférését rendszeresen felül lehet vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzájuk. Az Azure Virtual Machines használatakor át kell tekintenie a helyi biztonsági csoportokat és a felhasználókat, hogy ne legyenek olyan váratlan fiókok, amelyek veszélyeztethetik a rendszer biztonságát.

* [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: a Azure Active Directory diagnosztikai beállításainak konfigurálása a naplók és a bejelentkezési naplók log Analytics-munkaterületre való elküldéséhez. Emellett a Azure Monitor segítségével tekintheti át a naplókat, és lekérdezéseket hajthat végre az Azure Virtual Machines szolgáltatásban.

* [Log Analytics munkaterület ismertetése](../azure-monitor/logs/log-analytics-tutorial.md)

* [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

* [Virtuális gépek figyelése az Azure-ban](../azure-monitor/vm/monitor-vm-azure.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory kockázatkezelési és identitás-védelmi funkcióinak használatával konfigurálhatja a tárolási fiók erőforrásaival kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához engedélyeznie kell az automatikus válaszokat az Azure Sentinel használatával.

* [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

* [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: olyan forgatókönyvek esetén, ahol a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez (például egy támogatási kérelem során), az Azure-beli virtuális gépek Ügyfélszéf használatával tekintse át és hagyja jóvá a vásárlói adatokhoz való hozzáférési kérelmeket.

* [A Ügyfélszéf ismertetése](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-beli virtuális gépek nyomon követését.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az erőforrásokat virtuális hálózattal/alhálózattal kell elválasztani, megfelelően címkézve, és egy hálózati biztonsági csoporton (NSG) vagy egy Azure Firewallon belül kell biztosítani. A bizalmas adatok tárolására és feldolgozására Virtual Machines a házirend és eljárás (ok) bekapcsolásával kikapcsolhatja azokat, ha nincsenek használatban.

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

**Útmutató**: külső gyártótól származó megoldás implementálása olyan hálózati területeken, amelyek figyelik a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztást küldenek az információs biztonsági szakembereknek.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli az ügyfelek adatvesztése és a kitettség elleni védelem érdekében. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Windows rendszerű, Virtual Machines (VM)-re irányuló, a (z) rendszert futtató, illetve között áthaladó adatok több módon is titkosítva vannak, a kapcsolat természetétől függően, például egy RDP-vagy SSH-munkamenetben lévő virtuális géphez való csatlakozáskor.

A Microsoft a Transport Layer Security (TLS) protokollt használja az adatvédelemhez, amikor a Cloud Services és az ügyfelek között utazik.

* [Átvitel közbeni titkosítás a virtuális gépeken](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított bizalmas információk azonosítására egy külső féltől származó aktív felderítési eszköz használatával, beleértve a helyszíni vagy a távoli szolgáltatónál lévőket, valamint a szervezet bizalmas információinak leltározását.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával elkülönítheti a feladatait a csapaton belül, és csak a virtuális GÉPEN (VM) a feladataik elvégzéséhez szükséges hozzáférést biztosíthat a felhasználóknak. Ahelyett, hogy mindenki számára nem korlátozott engedélyeket adna a virtuális géphez, csak bizonyos műveleteket engedélyezhet. A virtuális gép hozzáférés-vezérlését az Azure CLI vagy a Azure PowerShell használatával konfigurálhatja a Azure Portal.

* [Azure RBAC-vel](../role-based-access-control/overview.md)

* [Beépített Azure-szerepkörök](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: egy külső gyártótól származó eszköz, például egy automatizált gazdagép-alapú adatvesztés-megelőzési megoldás alkalmazása a hozzáférés-vezérlés kikényszeríthető az adatvesztés kockázatának enyhítése érdekében.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Virtual Machines (VM) virtuális lemezeinek tárolása a kiszolgálóoldali titkosítás vagy az Azure Disk Encryption (ADE) használatával történik. A Azure Disk Encryption kihasználja a Linux DM-Crypt funkcióját, hogy a felügyelt lemezeket a vendég virtuális gépen lévő ügyfél által felügyelt kulcsokkal titkosítsa. Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t, mivel lehetővé teszi a virtuális gépekhez tartozó operációsrendszer-típusok és-lemezképek használatát a Storage szolgáltatásban tárolt adattitkosítással.

* [Virtual Machine Scale Sets Azure Disk Encryption](./disk-encryption-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel a rendszer a virtuális gépek méretezési csoportjaira és a kapcsolódó erőforrásokra vonatkozó módosításokat hajt végre.

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

* [Azure Storage Analytics-naplózás](../storage/common/storage-analytics-logging.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure Security Center ajánlásainak követése az Azure-Virtual Machines a sebezhetőségi felmérések végrehajtása során. A virtuális gépek sebezhetőségi felmérésének elvégzéséhez használja az Azure Security által ajánlott vagy harmadik féltől származó megoldást.

* [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: az operációs rendszer által támogatott verziók és a megosztott képtárban tárolt Egyéni rendszerképek automatikus operációsrendszer-frissítéseinek engedélyezése.

* [A virtuálisgép-méretezési csoportok automatikus operációs rendszerének frissítése az Azure-ban](./virtual-machine-scale-sets-automatic-upgrade.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: az Azure Virtual Machine Scale sets (VMSS) képes az operációs rendszer rendszerképének automatikus frissítésére. Használhatja az Azure desired State Configuration (DSC) bővítményt a VMSS alapul szolgáló virtuális gépekhez. A DSC használatával online állapotba állíthatja a virtuális gépeket, így azok futtatják a kívánt szoftvert.

* [Virtual Machine Scale Sets használata az Azure DSC bővítménnyel](./virtual-machine-scale-sets-dsc.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a vizsgálati eredmények konzisztens időközönkénti exportálása és az eredmények összehasonlítása annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. Ha Azure Security Center által javasolt sebezhetőségi kezelési javaslatot használ, az ügyfél a kiválasztott megoldás portálján megtekintheti a korábbi vizsgálati adataikat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata.

* [A Azure Security Center biztonsági pontszámának megismerése](../security-center/secure-score-security-controls.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetéseken belüli összes erőforrást (beleértve a virtuális gépeket is). Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

* [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

* [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, amelyekkel a metaadatok logikailag rendezhetők a besorolások alapján.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ahol szükséges, Virtual Machines méretezési csoportokat és a kapcsolódó erőforrásokat rendezheti és követheti nyomon. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a szervezeti igényeknek megfelelően hozzon létre egy leltárt a jóváhagyott Azure-erőforrásokról és jóváhagyott szoftverekről a számítási erőforrásokhoz.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat. Ez magas biztonsági alapú környezetekben, például a Storage-fiókokkal is segít.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: a Azure Automation teljes körű irányítást biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során. Az Azure-beli virtuális gépek Leltározásával automatizálhatja a Virtual Machines összes szoftverével kapcsolatos információk gyűjtését. Megjegyzés: a szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A telepítés dátumának és egyéb információinak eléréséhez az ügyfélnek a vendég szintű diagnosztika engedélyezéséhez és a Windows-eseménynaplók Log Analytics munkaterületre való bekapcsolásához van szükség.

Jelenleg az adaptív alkalmazások vezérlői nem érhetők el Virtual Machine Scale Sets számára.

* [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

* [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: a Azure Automation teljes körű irányítást biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során. Change Tracking használatával azonosíthatja a Virtual Machines telepített összes szoftvert. A jogosulatlan szoftverek eltávolításához saját folyamatot alkalmazhat, vagy használhatja Azure Automation állapot konfigurációját.

* [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

* [A környezet változásainak követése a Change Tracking megoldással](../automation/change-tracking/overview.md)

* [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: jelenleg az adaptív alkalmazás-vezérlőelemek nem érhetők el Virtual Machine Scale Setshoz. A külső gyártótól származó szoftverek használatával a használatot csak jóváhagyott alkalmazások számára vezérelheti.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: jelenleg az adaptív alkalmazás-vezérlőelemek nem érhetők el Virtual Machine Scale Setshoz. Harmadik féltől származó megoldás implementálása, ha ez nem felel meg a szervezet követelményének.

* [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

* [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: a parancsfájlok típusától függően használhat operációs rendszer-specifikus konfigurációkat vagy harmadik féltől származó erőforrásokat, amelyekkel korlátozhatja, hogy a felhasználók képesek legyenek parancsfájlokat végrehajtani az Azure számítási erőforrásokon belül.

* [A PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az Azure-környezetben üzembe helyezett magas kockázatú alkalmazások elkülöníthetők a virtuális hálózatok, az alhálózatok, az előfizetések, a felügyeleti csoportok stb. számára, és megfelelően biztonságosak a Azure Firewall, a webalkalmazási TŰZFAL (WAF) vagy a hálózati biztonsági csoport (NSG) használatával.

* [Virtuális hálózatok és virtuális gépek az Azure-ban](../virtual-machines/network-overview.md)

* [Azure Firewall áttekintése](../firewall/overview.md)

* [Webalkalmazási tűzfal – áttekintés](../web-application-firewall/overview.md)

* [Hálózati biztonság – áttekintés](../virtual-network/network-security-groups-overview.md)

* [Az Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)

* [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)

* [Útmutató az előfizetéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy vagy Azure Security Center használata az összes Azure-erőforrás biztonsági konfigurációjának fenntartásához. Emellett Azure Resource Manager lehetősége van a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni, hogy a konfigurációk megfeleljenek a vállalat biztonsági követelményeinek, és meghaladják azokat.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [A virtuálisgép-sablon letöltésével kapcsolatos információk](/previous-versions/azure/virtual-machines/windows/download-template)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: Azure Security Center javaslat használata [a Virtual Machines biztonsági konfigurációjában található biztonsági rések szervizelése] a biztonsági konfigurációk minden számítási erőforráson való fenntartásához.

* [Azure Security Center javaslatok figyelése](../security-center/security-center-recommendations.md)

* [Azure Security Center javaslatok szervizelése](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: Azure Resource Manager-sablonok és Azure-házirendek használata a Virtual Machines méretezési csoportokhoz társított Azure-erőforrások biztonságos konfigurálásához. A Azure Resource Manager-sablonok a virtuális gépek Azure-erőforrásokkal és egyéni sablonnal való üzembe helyezéséhez használt JSON-alapú fájlok, amelyeket meg kell őrizni. A Microsoft végzi el a karbantartást az alapsablonokon. Az Azure-szabályzat [deny] és a [telepítés, ha nem létezik] használatával kényszerítheti ki az Azure-erőforrások biztonságos beállításait.

* [Azure Resource Manager sablonok létrehozásával kapcsolatos információk](../virtual-machines/windows/ps-template.md)

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: az Azure Virtual Machines (VM) biztonságos konfigurációjának fenntartására több lehetőség áll rendelkezésre az üzembe helyezéshez:

1. Azure Resource Manager sablonok: ezek olyan JSON-alapú fájlok, amelyek a virtuális gépek Azure Portalból való üzembe helyezéséhez szükségesek, és az egyéni sablont is karban kell tartani. A Microsoft végzi el a karbantartást az alapsablonokon.

2. Egyéni virtuális merevlemez (VHD): bizonyos esetekben előfordulhat, hogy olyan egyéni VHD-fájlokat kell használnia, mint például a más módon nem felügyelhető összetett környezetek kezelése.

3. Azure Automation állapot konfigurálása: miután telepítette az alaprendszert, ez a beállítás részletesebben szabályozható a beállításokban, és az Automation-keretrendszer segítségével kényszeríthető ki.

A legtöbb esetben a Microsoft Base VM-sablonok a Azure Automation kívánt állapot-konfigurációval együtt segíthetnek a biztonsági követelmények teljesítésében és fenntartásában.

* [A virtuálisgép-sablon letöltésével kapcsolatos információk](/previous-versions/azure/virtual-machines/windows/download-template)

* [Az ARM-sablonok létrehozásával kapcsolatos információk](../virtual-machines/windows/ps-template.md)

* [Egyéni virtuális merevlemez feltöltése az Azure-ba](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat, a kívánt állapot-konfigurációs parancsfájlokat stb.  Az Azure-DevOps felügyelt erőforrások, például a kód, a buildek és a munkahelyi nyomkövetés eléréséhez engedélyekkel kell rendelkeznie az adott erőforrásokhoz. A legtöbb engedély a beépített biztonsági csoportokon keresztül adható meg az engedélyek és hozzáférés című témakörben leírtak szerint. Az Azure DevOps integrált, Active Directory vagy a TFS-vel integrált integrációval rendelkező felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára is engedélyezheti vagy megtagadhatja az engedélyeket.

* [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: Ha egyéni lemezképeket (például virtuális merevlemezt) használ, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez.

* [Az Azure RBAC ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Az Azure RBAC konfigurálása](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy kihasználása a virtuális gépek rendszerkonfigurációinak riasztására, naplózására és betartatására. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: Azure Automation állapot-konfiguráció a kívánt állapot-konfigurációs (DSC) csomópontok egyike a felhőben vagy a helyszíni adatközpontban. Lehetővé teszi, hogy gyorsan és könnyen, egy központi, biztonságos helyen lehessen méretezni a több ezer gépen. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják.

* [Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](../automation/automation-dsc-onboarding.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center kihasználása az Azure-beli virtuális gépek alapkonfigurációjának megkereséséhez. Az automatikus konfiguráció további módszereit Azure Automation állapot konfigurációjának használatával is elvégezheti.

* [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Azure Automation állapot konfigurációjának első lépései](../automation/automation-dsc-getting-started.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: Azure Automation állapot-konfiguráció a kívánt állapot-konfigurációs (DSC) csomópontok egyike a felhőben vagy a helyszíni adatközpontban. Lehetővé teszi, hogy gyorsan és könnyen, egy központi, biztonságos helyen lehessen méretezni a több ezer gépen. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják.

* [Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](../automation/automation-dsc-onboarding.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében.

* [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Key Vault létrehozása](../key-vault/general/quick-create-portal.md)

* [Hitelesítés Key Vault](../key-vault/general/authentication.md)

* [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure ad-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

* [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

* [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: a Microsoft antimalware használata az Azure Windows rendszerű virtuális gépekhez az erőforrások folyamatos monitorozásához és védelméhez. Szüksége lesz egy külső gyártótól származó eszközre a kártevő szoftverek elleni védelemhez az Azure Linux rendszerű virtuális gépen.

* [A Microsoft antimalware konfigurálása Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: az Azure-beli virtuális gépekre nem alkalmazható, mivel ez egy számítási erőforrás.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: nem alkalmazható

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: a Windows rendszerű virtuális gépek telepítésekor az Azure-hoz készült Microsoft antimalware alapértelmezés szerint automatikusan telepíti a legújabb aláírás-, platform-és motor-frissítéseket. Kövesse az Azure Security Center: "számítási alkalmazások" című témakörben található javaslatokat &amp; , hogy az összes végpont naprakész legyen a legújabb aláírásokkal. A Windows operációs rendszer további biztonsággal biztosítható, hogy a Microsoft Defender komplex veszélyforrások elleni védelmi szolgáltatásával, amely integrálható a Azure Security Centersal, a vírus-vagy kártevő-alapú támadások kockázatát korlátozza.

Szüksége lesz egy külső gyártótól származó eszközre a kártevő szoftverek elleni védelemhez az Azure Linux rendszerű virtuális gépen.

* [A Microsoft antimalware telepítése Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [A Microsoft antimalware konfigurálása Cloud Services és Virtual Machines](../virtual-machines/security-recommendations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: pillanatkép készítése az Azure virtuálisgép-méretezési csoport példányáról vagy a példányhoz csatolt felügyelt lemezről a PowerShell vagy a REST API-k használatával. A biztonsági mentési parancsfájlok rendszeres időközönként történő végrehajtásához Azure Automation is használhatja.

* [A virtuálisgép-méretezési csoport példányai és a felügyelt lemez pillanatképének készítése](./virtual-machine-scale-sets-faq.md#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Bevezetés a Azure Automationba](../automation/automation-intro.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: pillanatképek létrehozása az Azure-beli virtuális gépekről vagy a példányokhoz csatolt felügyelt lemezekről a PowerShell vagy a REST API-k használatával. Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül.

Engedélyezze Azure Backup és a célként megadott Azure-Virtual Machines (VM), valamint a kívánt gyakoriságot és megőrzési időt. Ez magában foglalja a rendszerállapot teljes biztonsági mentését. Ha az Azure Disk Encryption szolgáltatást használja, az Azure virtuális gép biztonsági mentése automatikusan kezeli az ügyfél által felügyelt kulcsok biztonsági mentését.

* [Biztonsági mentés titkosítást használó Azure-beli virtuális gépeken](../backup/backup-azure-vms-encryption.md)

* [Az Azure virtuális gépek biztonsági mentésének áttekintése](../backup/backup-azure-vms-introduction.md)

* [A virtuálisgép-méretezési csoport példányai és a felügyelt lemez pillanatképének készítése](./virtual-machine-scale-sets-faq.md#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a felügyelt lemez Azure Backupon belüli helyreállításának rendszeres időközönkénti elvégzése. Ha szükséges, tesztelje a visszaállítási tartalmat egy elkülönített virtuális hálózatra vagy előfizetésre. Az ügyfél által felügyelt kulcsok biztonsági másolatának visszaállítását.

Ha az Azure Disk Encryption szolgáltatást használja, visszaállíthatja a virtuálisgép-méretezési csoportokat a lemez titkosítási kulcsaival. A lemezes titkosítás használata esetén visszaállíthatja az Azure-beli virtuális gépet a lemez titkosítási kulcsaival.

* [Biztonsági mentés titkosítást használó Azure-beli virtuális gépeken](../backup/backup-azure-vms-encryption.md)

* [Lemez visszaállítása és helyreállított virtuális gép létrehozása az Azure-ban](../backup/tutorial-restore-disk.md)

* [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Az Azure-Virtual Machine Scale Sets lemezes titkosításának engedélyezése](./disk-encryption-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a felügyelt lemez törlési védelmének engedélyezése zárolások használatával. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze Soft-Delete és törölje a védelmet a Key Vaultban.

* [Erőforrások zárolása a váratlan módosítások megelőzése érdekében](../azure-resource-manager/management/lock-resources.md)

* [Azure Key Vault a Soft-delete és a Purge Protection áttekintése](../key-vault/general/soft-delete-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

* [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [A NIST számítógépes biztonsági incidensek kezelésének útmutatója a saját incidensekre vonatkozó válaszadási terv létrehozásához nyújtott támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Az Azure Security Center monitorozása**: Igen

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

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

* [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

* [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)