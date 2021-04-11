---
title: Azure Load Balancer Azure biztonsági alapterve
description: A Azure Load Balancer biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bffc9eb3e75dda2b04ad4118d1f599f85a0013c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590140"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure Load Balancer Azure biztonsági alapterve

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 1,0-es verziójának](../security/benchmarks/overview-v1.md) útmutatását alkalmazza Microsoft Azure Load Balancerre. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Load Balancer vonatkozó kapcsolódó útmutatás. A Azure Load Balancer nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Azure Load Balancer teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Load Balancer biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: a belső Azure Load Balancer használata, hogy csak bizonyos virtuális hálózatokon vagy összeállított virtuális hálózatokon lévő, az internet felé irányuló adatforgalmat engedélyezzen a háttérbeli erőforrásokhoz. Külső Load Balancer implementálása a forrásoldali hálózattal

Címfordítás (SNAT) – a háttérbeli erőforrások IP-címeinek maszkolása a közvetlen Internet-expozíció elleni védelem érdekében.

Az Azure kétféle Load Balancer ajánlatot, valamint standard és alapszintű szolgáltatásokat kínál. Használja a standard Load Balancer az összes éles számítási feladathoz. Hálózati biztonsági csoportok implementálása és csak az alkalmazás megbízható portjaihoz és IP-címtartományok eléréséhez való hozzáférés engedélyezése. Ha nincs olyan hálózati biztonsági csoport rendelve a háttérbeli alhálózathoz vagy a háttérbeli virtuális gépek hálózati ADAPTERéhez, akkor a terheléselosztó nem engedélyezi a forgalmat ezen erőforrások számára. A standard Load Balancer szolgáltatással a kimenő NAT definiálása hálózati biztonsági csoporttal. Tekintse át ezeket a kimenő szabályokat a kimenő kapcsolatok viselkedésének finomhangolásához.

Az éles számítási feladatokhoz a standard Load Balancer használata ajánlott, és általában az alapszintű Load Balancer csak teszteléshez használható, mivel az alapszintű típus alapértelmezés szerint nyitott az internetről, és nem igényel hálózati biztonsági csoportokat a művelethez.

- [Kimenő kapcsolatok az Azure-ban](load-balancer-outbound-connections.md)

- [Azure nyilvános Load Balancer frissítése](upgrade-basic-standard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

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

- [A kimenő kapcsolatok statisztikájának Hogyan](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az internetkapcsolatot és az érvényes forrás IP-címeket explicit módon definiálja a kimenő szabályok és hálózati biztonsági csoportok használatával a Microsoft által a webalkalmazások védelmére szolgáló fenyegetési intelligencia használatára a Load Balancer.

- [A Azure Firewall integrálása](../firewall/integrate-lb.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

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

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Network Watcher csomagok rögzítésének engedélyezése a rendellenes tevékenységek kivizsgálásához.

- [Network Watcher példány létrehozása](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Marketplace-ről szóló ajánlat megvalósítása, amely támogatja az azonosítók/IP-címek funkciót a hasznos adattartalom-ellenőrzési képességekkel a Load Balancer környezetében. 

Ha a hasznos adatok ellenőrzése nem követelmény, akkor Azure Firewall veszélyforrások felderítésére van szükség. Azure Firewall veszélyforrások felderítésére szolgáló szűrés a riasztások és/vagy az ismert kártékony IP-címek és tartományok felé irányuló, illetve azokból való adatforgalom letiltására szolgál. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy blokkolhatja a kártékony forgalmat.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: az internetkapcsolatot és az érvényes forrás IP-címeket explicit módon definiálja Load Balancer a kimenő szabályok és hálózati biztonsági csoportok segítségével, hogy a Microsoft fenyegetés-felderítési funkcióit a webalkalmazások védelmére használja.

- [A Azure Firewall integrálása](../firewall/integrate-lb.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: adott IP-címek helyett a szolgáltatási címkék használata biztonsági szabályok létrehozásakor. Adja meg a szolgáltatási címke nevét egy szabály forrás vagy cél mezőjében a megfelelő szolgáltatás forgalmának engedélyezéséhez vagy megtagadásához. 

A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. 

Alapértelmezés szerint minden hálózati biztonsági csoport tartalmazza az AzureLoadBalancer az állapot-mintavételi forgalom engedélyezésére szolgáló szolgáltatási címkét. 

Tekintse meg az Azure dokumentációját a hálózati biztonsági csoportokra vonatkozó szabályokban használható összes szolgáltatási címke esetében.

- [Elérhető szolgáltatás címkéi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával.

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például az Azure Resources Manager-sablonokat, az Azure RBAC-vezérlőket és a szabályzatokat egyetlen terv definíciójában. 

Alkalmazza a tervet az új előfizetésekre, és állítsa be a szabályozást és a felügyeletet a verziószámozáson keresztül.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a hálózatkezeléshez](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használjon erőforrás-címkéket a hálózati biztonsági csoportokhoz és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. 

A "Description" (Leírás) mező használatával dokumentálhatja azokat a szabályokat, amelyek engedélyezik a hálózatra irányuló adatforgalmat az egyes hálózati biztonsági csoportok szabályainak.

A címkézéssel kapcsolatos beépített Azure Policy-definíciók (például a "címke és az érték megkövetelése") implementálása, amely biztosítja, hogy minden erőforrás címkével és a meglévő címkézetlen erőforrások értesítésével legyen létrehozva.

A Azure PowerShell vagy az Azure CLI használatával kereshet vagy végezhet műveleteket az erőforrásokon a címkék alapján.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Azure-Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [Hálózati forgalom szűrése hálózati biztonsági csoport szabályaival](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti az erőforrás-konfigurációkat, és felderítheti az Azure-erőforrások módosításait. 

Riasztásokat hozhat létre a Azure Monitorban, hogy értesítést kapjon a kritikus erőforrások megváltozásakor.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

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

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

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

- [Az Azure Monitor naplózza a nyilvános Alapszintű Load Balancert](load-balancer-monitor-log.md)

- [Tevékenységek naplóinak megtekintése az erőforrásokon végzett műveletek figyeléséhez](../azure-resource-manager/management/view-activity-logs.md)

- [Többdimenziós mérőszámok programozott módon történő beolvasása API-kon keresztül](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a tevékenység naplója alapértelmezés szerint engedélyezve van, és 90 napig őrzi meg az Azure Eseménynapló-tárolójában. Állítsa be az Log Analytics munkaterület megőrzési időtartamát a szervezet megfelelőségi szabályainak megfelelően Azure Monitorban. Azure Storage-fiókokat használhat hosszú távú és archiválási tároláshoz.

- [Tevékenységek naplóinak megtekintése az erőforrásokkal kapcsolatos műveletek figyeléséhez cikk](../azure-resource-manager/management/view-activity-logs.md)

- [Az adatmegőrzési időszak módosítása Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: standard Load Balancer-erőforrások figyelése, kezelése és hibakeresése a Azure Portal Load Balancer lapján és a Resource Health oldalon Azure monitor alatt. Az elérhető biztonsági mérőszámok a forrás hálózati címfordítással (SNAT) kapcsolatos kapcsolatokra, portokra vonatkozó információkat tartalmaznak. Emellett elérhetők a következő metrikák is: SYN (szinkronizálás) csomagok és csomagok számlálói. 

A Azure Monitor segítségével tekintheti át a végpont állapotának mintavételi állapotát a standard, külső és belső, terheléselosztó esetében használt többdimenziós metrikákkal. Ezeket a metrikákat programozott módon, API-kon keresztül kell összegyűjteni, és az összes metrika lehetőséggel egy Storage-fiókba kell írni.

A belső alapszintű terheléselosztó számára nem érhetők el Azure Monitor naplók. 

A Azure Monitor használatával megtekintheti az állapot-mintavételi állapot összevont állapotát az alapszintű külső Load Balancer számára, például a háttérbeli példányok számát, amely nem fogadja a Load Balancertól érkező kéréseket az állapot-mintavételi hibák miatt. 

Az Azure Operational Insights naplózásának megvalósítása a terheléselosztó állapotával kapcsolatos statisztikák biztosításához. 

A Tevékenységnaplók használatával megtekintheti a riasztásokat, és figyelheti a műveleteket az erőforrásokon és azok állapotát az Azure-előfizetésekben. A Tevékenységnaplók alapértelmezés szerint engedélyezve vannak, és a Azure Portal megtekinthetők. 

Használja a Microsoft Power BIt az Azure-beli naplók tartalmának csomagjával, és elemezze az adatokat előre konfigurált irányítópultokkal. Az üzleti igényeknek megfelelően testre szabhatja a nézeteket. 

Adatfolyam-naplók az Event hub-ba vagy egy Log Analytics munkaterületre. Az Azure Blob Storage-ból is kivonhatók, és különböző eszközökön, például Excelben és Power BIban is megtekinthetők. Engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-hez való hozzáférését.

- [A Load Balancer állapotadat-mintavételei](load-balancer-custom-probe-overview.md)

- [Azure Monitor REST API](/rest/api/monitor)

- [Metrikák beolvasása REST API használatával](/rest/api/monitor/metrics/list)

- [Standard Load Balancer diagnosztika mérőszámokkal, riasztásokkal és erőforrás-állapottal](load-balancer-standard-diagnostics.md)

- [Az Azure Monitor naplózza a nyilvános Alapszintű Load Balancert](load-balancer-monitor-log.md)

- [A terheléselosztó metrikáinak megtekintése a Azure Portalban](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Security Center használata log Analytics munkaterülettel a biztonsági naplók és események Load Balancer kapcsolatos rendellenes tevékenység figyelésére és riasztására.

Az Azure Sentinel vagy egy harmadik féltől származó SIEM-eszköz számára engedélyezheti és folytathatja a helyszíni adatgyűjtést.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrások, például a Load Balancer szerepkör-hozzárendeléseken keresztüli hozzáférésének kezelését. Rendelje hozzá ezeket a szerepköröket a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz.

Az Azure CLI, Azure PowerShell vagy a Azure Portal eszközökkel előre definiált és beépített szerepköröket készíthet bizonyos erőforrásokhoz.

- [Címtárbeli szerepkör beszerzése Azure Active Directoryban (Azure AD) a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze az Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure hálózati erőforrásainak kezeléséhez és eléréséhez konfigurált, többtényezős hitelesítéssel rendelkező emelt szintű hozzáférésű munkaállomások (Paw) használata. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](/security/compass/privileged-access-devices)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: csak a jóváhagyott helyekről származó Azure-erőforrások kezelése

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: az Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként a szolgáltatásaihoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz, valamint a felhasználók hitelesítő adatainak tárolásához és kiszűréséhez is.  

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) használata az elavult fiókok felderítését segítő naplók biztosításához. 

Az Azure Identity hozzáférési felülvizsgálatok a csoporttagságok, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések hatékony kezelése érdekében végezhetők el. A felhasználói hozzáférés rendszeres felülvizsgálata szükséges ahhoz, hogy csak az aktív felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: az Azure Active Directory (Azure ad) bejelentkezési tevékenység, a naplózási és a kockázati Eseménynapló-források integrálása bármely Siem vagy monitoring eszközzel a hozzáférése alapján.

Az Azure AD-felhasználói fiókok diagnosztikai beállításainak létrehozásával és a naplók és bejelentkezési naplók Log Analytics munkaterületre való elküldésével egyszerűsítheti ezt a folyamatot. Minden kívánt riasztás konfigurálható Log Analytics munkaterületen belül.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory (Azure ad) kockázatkezelési és identitás-védelmi funkcióinak használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Minden további vizsgálathoz betöltheti az összes adatot az Azure Sentinelbe.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: gondoskodjon arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek legyenek a TLS 1,2 vagy újabb egyeztetésre.

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

- [A titkosítás ismertetése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: az Azure RBAC segítségével szabályozhatja a Load Balancer erőforrásaihoz való hozzáférést.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: a Load Balancer egy olyan továbbítási szolgáltatás, amely nem tárolja az ügyféladatokat. Ez a Microsoft által felügyelt mögöttes platform részét képezi. 

A Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú időt vesz igénybe. 

Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a kritikus Azure-erőforrások, például a fontos éles üzemi számítási feladatokhoz használt terheléselosztó esetében változnak.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésekben lévő összes erőforrást (például a számítási, tárolási, hálózati, portokat, protokollokat stb.). A Azure Resource Manager az aktuális erőforrások létrehozásához és használatához ajánlott.

Ellenőrizze a megfelelő (olvasási) engedélyeket a bérlőben, és sorolja fel az összes Azure-előfizetést és-erőforrást az előfizetésekben.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra metaadatokkal a besorolás szerint logikailag rendszerezve.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. 

Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy az előfizetések jogosulatlan erőforrásainak törlése időben megtörténjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: hozza létre a jóváhagyott Azure-erőforrások listáját a szervezeti igényei szerint, amelyet engedélyezési mechanizmusként használhat. Ez lehetővé teszi a szervezet számára az újonnan elérhető Azure-szolgáltatások bevezetését, miután azokat a szervezet tipikus biztonsági értékelési folyamatai hivatalosan felülvizsgálták és jóváhagyták.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrásokra vonatkozóan.

Erőforrások lekérdezése és felderítése az Azure Resource Graph-ban a tulajdonos előfizetések között. 

Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Azure Policy minta – beépített virtuális hálózatok](/azure/virtual-network/policy-samples)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure Active Directory (Azure ad) feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra a "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az üzleti műveletekhez szükséges szoftverek, de nagyobb kockázatot jelenthetnek a szervezet számára, el kell különíteni a saját virtuális gépén és/vagy virtuális hálózatán belül, és megfelelően biztonságossá kell tennie egy Azure Firewall vagy egy hálózati biztonsági csoporttal.

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)

- [Hálózati biztonsági csoport létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy-aliasok használata egyéni szabályzatok létrehozásához az Azure-erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. Beépített Azure Policy-definíciókat használhat.

A Azure Resource Manager lehetővé teszi a sablon exportálását JavaScript Object Notation (JSON), amelyet át kell tekinteni annak érdekében, hogy a konfigurációk megfeleljenek a szervezete biztonsági követelményeinek.

Exportálja Azure Resource Manager sablonokat JavaScript Object Notation (JSON) formátumba, és rendszeres időközönként ellenőrizze, hogy a konfigurációk megfelelnek-e a szervezet biztonsági követelményeinek.

A Security Center ajánlásainak megvalósítása az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.  Emellett Azure Resource Manager-sablonok használatával is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját. 

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például az egyéni Azure Policy-definíciók, Azure Resource Manager sablonok és a kívánt állapotú konfigurációs parancsfájlok.

Engedélyek megadása vagy megtagadása adott felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy az Azure AD-ben, ha az integrálva van a TFS-vel.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával.  Az Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az adott Azure Load Balancer erőforrásokhoz kapcsolódó beépített szabályzat-definíciók implementálása.  Emellett a Azure Automation használatával is telepítheti a konfigurációs módosításokat. a Azure Load Balancer erőforrásaihoz.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Security Center segítségével elvégezheti az Azure-erőforrások alapkonfigurációjának vizsgálatait, és Azure Policy riasztási és naplózási erőforrás-konfigurációkhoz.

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig. 

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md) 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. 

A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

Megadhatja az előfizetéseket címkékkel, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosítására és kategorizálására, különösen a bizalmas adatok feldolgozására.  

Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a hézagokat, majd szükség szerint módosítsa a választ. 

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. 

Security Center folyamatos exportálás funkciójának használata lehetővé teszi, hogy a riasztásokat és javaslatokat manuálisan vagy folyamatos, folyamatos módon exportálja. 

Használja a Security Center adatösszekötőt a riasztások Azure Sentinelbe való továbbításához.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja az Azure-erőforrások védelmére vonatkozó biztonsági riasztásokra és javaslatokra adott válaszokat.

- [A Munkafolyamat-automatizálás konfigurálása a biztonság megadásakor](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
