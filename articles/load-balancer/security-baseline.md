---
title: Azure Load Balancer Azure biztonsági alapterve
description: A Azure Load Balancer biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3701d9dde569432d8bf1910f04395df0b916b75a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575168"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure Load Balancer Azure biztonsági alapterve

A Microsoft Azure Load Balancer Azure biztonsági alapterve olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát. A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével. További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](../security/benchmarks/security-control-network-security.md)című témakörben talál.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: a belső Azure Load Balancer használata, hogy csak bizonyos virtuális hálózatokon vagy összeállított virtuális hálózatokon lévő, az internet felé irányuló adatforgalmat engedélyezzen a háttérbeli erőforrásokhoz. Hozzon létre egy külső Load Balancer a forrás hálózati címfordítással (SNAT), hogy a háttérbeli erőforrások IP-címeit a közvetlen Internet-expozíció elleni védelemhez társítsa.

Az Azure kétféle Load Balancer ajánlatot, valamint standard és alapszintű szolgáltatásokat kínál. Használja a standard Load Balancer az összes éles számítási feladathoz. Hálózati biztonsági csoportok implementálása és csak az alkalmazás megbízható portjaihoz és IP-címtartományok eléréséhez való hozzáférés engedélyezése. Ha nincs olyan hálózati biztonsági csoport rendelve a háttérbeli alhálózathoz vagy a háttérbeli virtuális gépek hálózati ADAPTERéhez, akkor a terheléselosztó nem engedélyezi a forgalmat ezen erőforrások számára. A standard Load Balancer szolgáltatással a kimenő NAT definiálása hálózati biztonsági csoporttal. Tekintse át ezeket a kimenő szabályokat a kimenő kapcsolatok viselkedésének finomhangolásához. 

Az éles számítási feladatokhoz a standard Load Balancer használata ajánlott, és általában az alapszintű Load Balancer csak teszteléshez használható, mivel az alapszintű típus alapértelmezés szerint nyitott az internetről, és nem igényel hálózati biztonsági csoportokat a művelethez. 

- [Kimenő kapcsolatok az Azure-ban](load-balancer-outbound-connections.md)

- [Azure nyilvános Load Balancer frissítése](./upgrade-basic-standard.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Load Balancer egy átmenő szolgáltatás, mivel a hálózati biztonsági csoportok a háttérbeli erőforrásokra alkalmazott szabályokra és az Internet-hozzáférés vezérlésére konfigurált kimenő szabályokra támaszkodik.

Tekintse át a standard Load Balancer konfigurált kimenő szabályokat a Load Balancer kimenő szabályok paneljén és a terheléselosztási szabályok panelen, ahol engedélyezheti az implicit kimenő szabályokat.

Figyelje a kimenő kapcsolatok számát, hogy nyomon követhesse, milyen gyakran éri el az erőforrások az internetet. 

Az Azure-hálózati erőforrások biztonságossá tételéhez használja a Security Centert, és kövesse a hálózati védelemre vonatkozó ajánlásokat.

Kövesse a háttérbeli erőforrások biztonsági javaslatait, és engedélyezze a hálózati biztonsági csoport folyamatábráit, és küldje el a naplókat egy Azure Storage-fiókba a naplózáshoz.

Küldje el a flow naplóit egy Log Analytics munkaterületre, majd a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalmi mintákba. A Traffic Analytics előnyei közé tartozik a hálózati tevékenységek vizualizációja, a gyakori és a biztonsági fenyegetések azonosítása, a forgalomban rejlő minták megértése, valamint a hálózati hibás konfigurációk meghatározása.

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

- [A kimenő kapcsolatok statisztikájának Hogyan](./load-balancer-standard-diagnostics.md#how-do-i-check-my-outbound-connection-statistics)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az internetkapcsolatot és az érvényes forrás IP-címeket explicit módon definiálja a kimenő szabályok és hálózati biztonsági csoportok használatával a Microsoft által a webalkalmazások védelmére szolgáló fenyegetési intelligencia használatára a Load Balancer.

- [A Azure Firewall integrálása](../firewall/integrate-lb.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: engedélyezze az Azure Distributed szolgáltatásmegtagadás (DDoS) standard szintű védelmét az Azure Virtual Network a DDOS-támadások elleni védelem érdekében. 

Azure Firewall üzembe helyezése minden szervezet hálózati határain, és a fenyegetésekkel kapcsolatos intelligencia-alapú Szűrés engedélyezve van, és úgy van beállítva, hogy a "riasztás és megtagadás" a kártékony hálózati forgalmat.

 

Security Center veszélyforrások elleni védelem használatával észlelheti az ismert kártékony IP-címekkel folytatott kommunikációt. 

A standard Load Balancer úgy van kialakítva, hogy alapértelmezés szerint biztonságos legyen, és egy magán-és elkülönített Virtual Network része legyen. A bejövő folyamatoknál zárva van, kivéve, ha a hálózati biztonsági csoportok nem engedélyezik az engedélyezett forgalom explicit módon történő engedélyezését, valamint az ismert kártékony IP-címek letiltását. Ha a virtuális gép erőforrásának alhálózatán vagy hálózati ADAPTERén található hálózati biztonsági csoport nem létezik a Load Balancer mögött, a forgalom nem jogosult az erőforrás elérésére. 

Azure Firewall üzembe helyezése a szervezet hálózati határain, és a fenyegetésekkel kapcsolatos intelligencián alapuló Szűrés engedélyezve van, és úgy van beállítva, hogy a rosszindulatú hálózati forgalom megakadályozza a rosszindulatú IP-címek elleni támadásokat. 

Útmutatást nyújt a Azure Firewall és az Load Balancer integrálásához.

Security Center veszélyforrások elleni védelem funkcióinak használatával észlelheti az ismert kártékony IP-címekkel folytatott kommunikációt. 

A Security Center (standard szint) a virtuális gépek igény szerinti elérését biztosítja, és az engedélyezett forrás IP-címeket konfigurálja úgy, hogy csak a jóváhagyott IP-címekről/tartományokról engedélyezze a hozzáférést.
 

A Security Center adaptív hálózatának megerősítése funkció segítségével a hálózati biztonsági csoportok konfigurációit a tényleges forgalom és a fenyegetések felderítése alapján korlátozhatja a portok és a forrás IP-címek korlátozására.
 

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall fenyegetés intelligencia-alapú szűrés](../firewall/threat-intel.md)

- [Fenyegetésvédelem az Azure Security Centerben](../security-center/azure-defender.md)

- [Felügyeleti portok védelme igény szerinti hozzáféréssel](../security-center/security-center-just-in-time.md)

- [Adaptív hálózati megerősítés Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Firewall integrálása a Load Balancer](../firewall/overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Network Watcher csomagok rögzítésének engedélyezése a rendellenes tevékenységek kivizsgálásához.

- [Network Watcher példány létrehozása](../network-watcher/network-watcher-create.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Marketplace-ről szóló ajánlat megvalósítása, amely támogatja az azonosítók/IP-címek funkciót a hasznos adattartalom-ellenőrzési képességekkel a Load Balancer környezetében. 

Ha a hasznos adatok ellenőrzése nem követelmény, akkor Azure Firewall veszélyforrások felderítésére van szükség. Azure Firewall veszélyforrások felderítésére szolgáló szűrés a riasztások és/vagy az ismert kártékony IP-címek és tartományok felé irányuló, illetve azokból való adatforgalom letiltására szolgál. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy blokkolhatja a kártékony forgalmat.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: az internetkapcsolatot és az érvényes forrás IP-címeket explicit módon definiálja Load Balancer a kimenő szabályok és hálózati biztonsági csoportok segítségével, hogy a Microsoft fenyegetés-felderítési funkcióit a webalkalmazások védelmére használja.

- [A Azure Firewall integrálása](../firewall/integrate-lb.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: adott IP-címek helyett a szolgáltatási címkék használata biztonsági szabályok létrehozásakor. Adja meg a szolgáltatási címke nevét egy szabály forrás vagy cél mezőjében a megfelelő szolgáltatás forgalmának engedélyezéséhez vagy megtagadásához. 

A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. 

Alapértelmezés szerint minden hálózati biztonsági csoport tartalmazza az AzureLoadBalancer az állapot-mintavételi forgalom engedélyezésére szolgáló szolgáltatási címkét. 

Tekintse meg az Azure dokumentációját a hálózati biztonsági csoportokra vonatkozó szabályokban használható összes szolgáltatási címke esetében.

- [Elérhető szolgáltatás címkéi](../virtual-network/service-tags-overview.md#available-service-tags)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával.

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például az Azure Resources Manager-sablonokat, az Azure RBAC-vezérlőket és a szabályzatokat egyetlen terv definíciójában. 

Alkalmazza a tervet az új előfizetésekre, és állítsa be a szabályozást és a felügyeletet a verziószámozáson keresztül.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használjon erőforrás-címkéket a hálózati biztonsági csoportokhoz és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. 

A "Description" (Leírás) mező használatával dokumentálhatja azokat a szabályokat, amelyek engedélyezik a hálózatra irányuló adatforgalmat az egyes hálózati biztonsági csoportok szabályainak.

A címkézéssel kapcsolatos beépített Azure Policy-definíciók (például a "címke és az érték megkövetelése") implementálása, amely biztosítja, hogy minden erőforrás címkével és a meglévő címkézetlen erőforrások értesítésével legyen létrehozva.

A Azure PowerShell vagy az Azure CLI használatával kereshet vagy végezhet műveleteket az erőforrásokon a címkék alapján.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Azure-Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [Hálózati forgalom szűrése hálózati biztonsági csoport szabályaival](../virtual-network/tutorial-filter-network-traffic.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti az erőforrás-konfigurációkat, és felderítheti az Azure-erőforrások módosításait. 

Riasztásokat hozhat létre a Azure Monitorban, hogy értesítést kapjon a kritikus erőforrások megváltozásakor.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: Tekintse át a kimenő szabályok és hálózati biztonsági csoportok módosításait a terheléselosztó számára az előfizetések tevékenységi naplójának megtekintésével. 

A belső gazdagép naplófájljainak megtekintésével biztosíthatja, hogy a háttérbeli erőforrások biztonságosak legyenek.

Ezeket a naplókat Log Analytics vagy egy másik tárolási platformra exportálhatja. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

A szervezeti üzleti követelmények alapján engedélyezheti és elküldheti ezeket az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Platform-tevékenységek naplói](../azure-monitor/essentials/activity-log.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: Tekintse át a vezérlő és a felügyeleti sík naplózási adatait, valamint az alapszintű Load Balancer tevékenységi naplóival rögzített naplózási információkat. Ezek a rögzítési beállítások alapértelmezés szerint engedélyezve vannak. 

A tevékenység-naplók segítségével figyelheti az erőforrásokon végzett műveleteket az összes tevékenység és az állapotuk megtekintéséhez. 

Határozza meg, hogy milyen műveletek történtek az előfizetésben lévő erőforrásokon a tevékenység naplójában: 

- a művelet elindítása
- a művelet bekövetkeztekor
- a művelet állapota

- más tulajdonságok értékei, amelyek segíthetnek a művelet megkutatásában

A Azure PowerShellon, az Azure parancssori felületen (CLI), az Azure REST API vagy a Azure Portalon keresztül kérhet le adatokat a tevékenység naplóból. 

Többdimenziós diagnosztika implementálása a standard Load Balancer konfigurációk képességeihez Azure Monitor.  Ezek közé tartoznak a biztonsági rendelkezésre álló mérőszámok, amelyek a forrás hálózati címfordítással (SNAT) kapcsolatos kapcsolatokra, portokra vonatkozó információkat tartalmazzák. További mérőszámok is elérhetők a SYN (szinkronizált) csomagok és a csomagok számlálói számára. 

Többdimenziós mérőszámok programozott módon történő beolvasása API-kon keresztül, és az összes metrika lehetőséggel megírhatók egy Storage-fiókba.

Az Azure audit a Microsoft Power BI segítségével elemezheti az adatokat az előre konfigurált irányítópultokkal, vagy testreszabhatja a nézeteket a követelmények alapján.

Adatfolyam-naplók az Event hub-ba vagy egy Log Analytics munkaterületre. Az Azure Blob Storage-ból is kivonhatók, és különböző eszközökön, például Excelben és Power BIban is megtekinthetők. 

Az üzleti igényeknek megfelelően engedélyezheti és elküldheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek szánt adatait.

- [Tekintse át ezt a cikket, amely részletesen ismerteti a naplózási műveletek a Resource Managerrel című témakörben ismertetett lépéseket.](../azure-resource-manager/management/view-activity-logs.md)

- [Az Azure Monitor naplózza a nyilvános Alapszintű Load Balancert](./load-balancer-monitor-log.md)

- [Tevékenységek naplóinak megtekintése az erőforrásokon végzett műveletek figyeléséhez](../azure-resource-manager/management/view-activity-logs.md)

- [Többdimenziós mérőszámok programozott módon történő beolvasása API-kon keresztül](./load-balancer-standard-diagnostics.md#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a tevékenység naplója alapértelmezés szerint engedélyezve van, és 90 napig őrzi meg az Azure Eseménynapló-tárolójában. Állítsa be az Log Analytics munkaterület megőrzési időtartamát a szervezet megfelelőségi szabályainak megfelelően Azure Monitorban. Azure Storage-fiókokat használhat hosszú távú és archiválási tároláshoz.

- [Tevékenységek naplóinak megtekintése az erőforrásokkal kapcsolatos műveletek figyeléséhez cikk](../azure-resource-manager/management/view-activity-logs.md)

- [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: standard Load Balancer-erőforrások figyelése, kezelése és hibakeresése a Azure Portal Load Balancer lapján és a Resource Health oldalon Azure monitor alatt. Az elérhető biztonsági mérőszámok a forrás hálózati címfordítással (SNAT) kapcsolatos kapcsolatokra, portokra vonatkozó információkat tartalmaznak. Emellett elérhetők a következő metrikák is: SYN (szinkronizálás) csomagok és csomagok számlálói. 

A Azure Monitor segítségével tekintheti át a végpont állapotának mintavételi állapotát a standard, külső és belső, terheléselosztó esetében használt többdimenziós metrikákkal. Ezeket a metrikákat programozott módon, API-kon keresztül kell összegyűjteni, és az összes metrika lehetőséggel egy Storage-fiókba kell írni.

A belső alapszintű terheléselosztó számára nem érhetők el Azure Monitor naplók. 

A Azure Monitor használatával megtekintheti az állapot-mintavételi állapot összevont állapotát az alapszintű külső Load Balancer számára, például a háttérbeli példányok számát, amely nem fogadja a Load Balancertól érkező kéréseket az állapot-mintavételi hibák miatt. 

Az Azure Operational Insights naplózásának megvalósítása a terheléselosztó állapotával kapcsolatos statisztikák biztosításához. 

A Tevékenységnaplók használatával megtekintheti a riasztásokat, és figyelheti a műveleteket az erőforrásokon és azok állapotát az Azure-előfizetésekben. A Tevékenységnaplók alapértelmezés szerint engedélyezve vannak, és a Azure Portal megtekinthetők. 

Használja a Microsoft Power BIt az Azure-beli naplók tartalmának csomagjával, és elemezze az adatokat előre konfigurált irányítópultokkal. Az üzleti igényeknek megfelelően testre szabhatja a nézeteket. 

Adatfolyam-naplók az Event hub-ba vagy egy Log Analytics munkaterületre. Az Azure Blob Storage-ból is kivonhatók, és különböző eszközökön, például Excelben és Power BIban is megtekinthetők. Engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-hez való hozzáférését.

- [A Load Balancer állapotadat-mintavételei](./load-balancer-custom-probe-overview.md)

- [Azure Monitor REST API](/rest/api/monitor)

- [Metrikák beolvasása REST API használatával](/rest/api/monitor/metrics/list)

- [Standard Load Balancer diagnosztika mérőszámokkal, riasztásokkal és erőforrás-állapottal](./load-balancer-standard-diagnostics.md)

- [Az Azure Monitor naplózza a nyilvános Alapszintű Load Balancert](./load-balancer-monitor-log.md)

- [A terheléselosztó metrikáinak megtekintése a Azure Portalban](./load-balancer-standard-diagnostics.md#view-your-load-balancer-metrics-in-the-azure-portal)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Security Center használata log Analytics munkaterülettel a biztonsági naplók és események Load Balancer kapcsolatos rendellenes tevékenység figyelésére és riasztására.

Az Azure Sentinel vagy egy harmadik féltől származó SIEM-eszköz számára engedélyezheti és folytathatja a helyszíni adatgyűjtést.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható a Azure Load Balancerra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható, mert a Azure Load Balancer olyan alapvető hálózati szolgáltatás, amely nem tesz elérhetővé DNS-lekérdezéseket.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: a Azure Load Balancer nem alkalmazható, mivel ez a javaslat a számítási erőforrásokra vonatkozik.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrások, például a Load Balancer szerepkör-hozzárendeléseken keresztüli hozzáférésének kezelését. Rendelje hozzá ezeket a szerepköröket a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. 

Az Azure CLI, Azure PowerShell vagy a Azure Portal eszközökkel előre definiált és beépített szerepköröket készíthet bizonyos erőforrásokhoz.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](../security/benchmarks/security-control-data-protection.md)című témakörben talál.*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: az Azure RBAC segítségével szabályozhatja a Load Balancer erőforrásaihoz való hozzáférést.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: a Load Balancer egy olyan továbbítási szolgáltatás, amely nem tárolja az ügyféladatokat. Ez a Microsoft által felügyelt mögöttes platform részét képezi. 

A Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú időt vesz igénybe. 

Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a kritikus Azure-erőforrások, például a fontos éles üzemi számítási feladatokhoz használt terheléselosztó esetében változnak.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésekben lévő összes erőforrást (például a számítási, tárolási, hálózati, portokat, protokollokat stb.). A Azure Resource Manager az aktuális erőforrások létrehozásához és használatához ajánlott. 

Ellenőrizze a megfelelő (olvasási) engedélyeket a bérlőben, és sorolja fel az összes Azure-előfizetést és-erőforrást az előfizetésekben.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra metaadatokkal a besorolás szerint logikailag rendszerezve.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. 

Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy az előfizetések jogosulatlan erőforrásainak törlése időben megtörténjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: hozzon létre egy listát a jóváhagyott Azure-erőforrásokról a szervezeti igényeknek megfelelően, amelyet az engedélyezési lista mechanizmusként használhat. Ez lehetővé teszi a szervezet számára az újonnan elérhető Azure-szolgáltatások bevezetését, miután azokat a szervezet tipikus biztonsági értékelési folyamatai hivatalosan felülvizsgálták és jóváhagyták.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrásokra vonatkozóan.

Erőforrások lekérdezése és felderítése az Azure Resource Graph-ban a tulajdonos előfizetések között. 

Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure ad feltételes hozzáférés használatával korlátozhatja a felhasználók interakcióját a Azure Resource Manager az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az üzleti műveletekhez szükséges szoftverek, de nagyobb kockázatot jelenthetnek a szervezet számára, el kell különíteni a saját virtuális gépén és/vagy virtuális hálózatán belül, és megfelelően biztonságossá kell tennie egy Azure Firewall vagy egy hálózati biztonsági csoporttal.

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)

- [Hálózati biztonsági csoport létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy-aliasok használata egyéni szabályzatok létrehozásához az Azure-erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. Beépített Azure Policy-definíciókat használhat.

A Azure Resource Manager lehetővé teszi a sablon exportálását JavaScript Object Notation (JSON), amelyet át kell tekinteni annak érdekében, hogy a konfigurációk megfeleljenek a szervezete biztonsági követelményeinek.

Exportálja Azure Resource Manager sablonokat JavaScript Object Notation (JSON) formátumba, és rendszeres időközönként ellenőrizze, hogy a konfigurációk megfelelnek-e a szervezet biztonsági követelményeinek. 

A Security Center ajánlásainak megvalósítása az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően. 

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.  Emellett Azure Resource Manager-sablonok használatával is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját. 

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például az egyéni Azure Policy-definíciók, Azure Resource Manager sablonok és a kívánt állapotú konfigurációs parancsfájlok. 

Engedélyek megadása vagy megtagadása adott felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha integrálva van az Azure DevOps, vagy Active Directory, ha integrálva van a TFS-vel.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával.  Az Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az adott Azure Load Balancer erőforrásokhoz kapcsolódó beépített szabályzat-definíciók implementálása.  Emellett a Azure Automation használatával is telepítheti a konfigurációs módosításokat. a Azure Load Balancer erőforrásaihoz.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Security Center segítségével elvégezheti az Azure-erőforrások alapkonfigurációjának vizsgálatait, és Azure Policy riasztási és naplózási erőforrás-konfigurációkhoz.

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](../security/benchmarks/security-control-incident-response.md)című témakörben talál.*

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. 

A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

Megadhatja az előfizetéseket címkékkel, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosítására és kategorizálására, különösen a bizalmas adatok feldolgozására.  

Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. 

Security Center folyamatos exportálás funkciójának használata lehetővé teszi, hogy a riasztásokat és javaslatokat manuálisan vagy folyamatos, folyamatos módon exportálja. 

Használja a Security Center adatösszekötőt a riasztások Azure Sentinelbe való továbbításához.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja az Azure-erőforrások védelmére vonatkozó biztonsági riasztásokra és javaslatokra adott válaszokat.

- [A Munkafolyamat-automatizálás konfigurálása a biztonság megadásakor](../security-center/workflow-automation.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)