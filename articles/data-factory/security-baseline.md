---
title: Azure Data Factory Azure biztonsági alapterve
description: Azure Data Factory Azure biztonsági alapterve
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 690dcca23aedb129090b3bf4f0da8874705b5fcc
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941464"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Azure Data Factory Azure biztonsági alapterve

Az Azure Data Factory Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: az [Azure biztonsági alaptervek áttekintése](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: Azure-SSIS INTEGRATION RUNTIME (IR) létrehozásakor lehetősége van csatlakoztatni egy virtuális hálózathoz. Ez lehetővé teszi, hogy Azure Data Factory bizonyos hálózati erőforrások, például egy hálózati biztonsági csoport (NSG) és egy terheléselosztó létrehozásához. Lehetősége van saját statikus nyilvános IP-címének megadására is, vagy Azure Data Factory létrehozhat egyet. A Azure Data Factory által automatikusan létrehozott NSG a 3389-es port alapértelmezés szerint minden forgalom számára nyitva van. A zárolást lenyomva győződjön meg arról, hogy csak a rendszergazdák férhetnek hozzá.

Self-Hosted IRs egy virtuális hálózaton belüli helyszíni gépen vagy Azure-beli virtuális gépen is üzembe helyezhető. Győződjön meg arról, hogy a virtuális hálózati alhálózat központi telepítése NSG van konfigurálva, hogy csak a rendszergazdai hozzáférést engedélyezze. A Azure-SSIS IR alapértelmezés szerint nem engedélyezte a (z) 3389 portot a Windows tűzfal minden egyes IR csomópontján a védelemhez. A virtuális hálózat által konfigurált erőforrásokat biztonságossá teheti, ha társít egy NSG az alhálózathoz, és beállítja a szigorú szabályokat.

Ha a privát hivatkozás elérhető, használja a privát végpontokat a Azure Data Factory-folyamathoz (például az Azure SQL Serverhoz) kapcsolódó erőforrások biztonságossá tételéhez. A privát kapcsolattal a virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinc hálózatán halad át, így nem kerül a nyilvános internetről.

* [Azure-SSIS IR létrehozása](./create-azure-ssis-integration-runtime.md)

* [Saját üzemeltetésű integrációs modul létrehozása és konfigurálása](./create-self-hosted-integration-runtime.md)

* [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

* [Biztonsági konfigurációval rendelkező NSG létrehozása](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz](./join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

* [Az Azure privát hivatkozásának megismerése](../private-link/private-link-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózati védelmi javaslatok szervizelése a Integration Runtime-telepítéshez társított virtuális hálózat és hálózati biztonsági csoport számára.

Emellett engedélyezze a hálózati biztonsági csoport (NSG) folyamatábráit a Integration Runtime-telepítés védelmét biztosító NSG, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózásához.

NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

* [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

* [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

* [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat az Azure apps szolgáltatáshoz vagy webalkalmazásokat üzemeltető számítási erőforrásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az elosztott szolgáltatásmegtagadási támadások elleni védelem érdekében a Integration Runtime központi telepítéshez társított virtuális hálózatokon DDoS Protection szabványokat engedélyezze. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

* [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

* [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: engedélyezze a hálózati biztonsági csoport (NSG) számára a Integration Runtime üzembe helyezését védő NSG, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózásához.

NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

* [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

* [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: Ha meg szeretné vizsgálni a Azure-SSIS IR kimenő forgalmát, átirányíthatja Azure-SSIS IRról a helyszíni tűzfalra kezdeményezett forgalmat az Azure ExpressRoute kényszerített bújtatásával vagy az Azure Marketplace-en keresztül az azonosítók/IP-címeket támogató hálózati virtuális berendezéssel (NVA). Ha a betörési észlelés és/vagy a tartalom-ellenőrzésen alapuló megelőzés nem követelmény, akkor az Azure Firewall a veszélyforrások felderítésére használható.

* [Azure-SSIS Integration Runtime csatlakoztatása egy virtuális hálózathoz](./join-azure-ssis-integration-runtime-virtual-network.md)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

* [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat az Azure apps szolgáltatáshoz vagy webalkalmazásokat üzemeltető számítási erőforrásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutatás**: hálózati biztonsági csoport (NSG) vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használja a Virtual Network szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha a szolgáltatási címke nevét (például DataFactoryManagement) adja meg egy szabály megfelelő forrás vagy cél mezőjében, engedélyezheti vagy megtagadhatja a bejövő forgalmat a megfelelő szolgáltatáshoz. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

* [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

* [Azure Data Factory adott szolgáltatási címkék ismertetése](./join-azure-ssis-integration-runtime-virtual-network.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure-beli adatfeldolgozó-példányokhoz társított hálózati beállítások és hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. DataFactory" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Azure Data Factory példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a hálózatkezeléssel vagy az Azure-beli adat-előállító példányokkal kapcsolatos beépített szabályzat-definíciókat is, például:
- DDoS Protection a standardot engedélyezni kell

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network) 

* [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a metaadatok és a logikai szervezet számára a Azure Data Factory-példányok hálózati biztonságával és forgalmával kapcsolatos erőforrásokhoz használható címkék használata.

A címkézéssel kapcsolatos beépített Azure Policy-definíciók bármelyikét használhatja, például: "a címke és az érték megkövetelése", hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Data Factory-példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

* [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Riasztások létrehozása a Azure Monitorban](../azure-monitor/platform/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrásokhoz használt időforrást, például Azure Data Factory a naplókban található időbélyegek esetében.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor segítségével a Azure Data Factory által generált biztonsági adatokat összesítve. A Azure Monitoron belül lekérdezheti a Log Analytics munkaterületet, amely a Azure Data Factory tevékenység naplófájljainak fogadására van konfigurálva. Az Azure Storage-fiókokat használhatja hosszú távú/archiválási naplók tárolására, illetve az olyan esemény-hubokra, amelyek más rendszerekre exportálják az adatexportálást.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események felügyeletének (SIEM) engedélyezését. A git használatával Azure Data Factory is integrálhat, így több verziókövetés előnyeit is kihasználhatja, például nyomon követheti és naplózhatja a módosításokat, és visszaállíthatja a hibákat tartalmazó módosításokat.

* [Diagnosztikai beállítások konfigurálása](../azure-monitor/platform/diagnostic-settings.md)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Verziókövetés a Azure Data Factory](./source-control.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a vezérlési síkok naplózásához engedélyezze az Azure-műveletnapló diagnosztikai beállításait, és küldje el a naplókat log Analytics munkaterületre, Azure Event Hubs vagy Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

Diagnosztikai beállításokkal konfigurálhatja a Azure Data Factory nem számítási erőforrásaihoz tartozó diagnosztikai naplókat, például a mérőszámokat és a folyamat által futtatott adatokat. A Azure Data Factory 45 napig tárolja a folyamat által futtatott adatfeldolgozást. Ha hosszabb ideig szeretné megőrizni az adatok mennyiségét, mentse a diagnosztikai naplókat egy Storage-fiókba a naplózáshoz vagy a manuális vizsgálathoz, és adja meg a megőrzési időt napokban. A naplókat átirányíthatja az Azure Event Hubsba, vagy elküldheti a naplókat egy Log Analytics-munkaterületre elemzés céljából.

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/platform/activity-log.md)

* [Azure Data Factory diagnosztikai naplók ismertetése](./monitor-using-azure-monitor.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális GÉPEN (VM) futtatja, akkor a Azure monitor használatával adatokat gyűjthet a virtuális gépről. A Log Analytics virtuálisgép-bővítmény telepítése lehetővé teszi, hogy a Azure Monitor adatokat gyűjtsön az Azure-beli virtuális gépekről. A Azure Security Center a Virtual Machines biztonsági eseménynaplóinak figyelését is lehetővé teszi. A biztonsági eseménynapló által generált adatmennyiség miatt a szolgáltatás alapértelmezés szerint nem tárolja azt.

Ha a szervezete meg szeretné őrizni a biztonsági eseménynapló adatait, az egy adatgyűjtési szinten tárolható, amelyen Log Analytics lehet lekérdezni.

* [Adatok gyűjtése az Azure Virtual Machinesról Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

* [Adatgyűjtés engedélyezése a Azure Security Centerban](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure Data Factory diagnosztikai beállításainak engedélyezése. Ha úgy dönt, hogy egy Log Analytics munkaterületen tárolja a naplókat, akkor a szervezet megfelelőségi előírásai szerint állítsa be a Log Analytics munkaterület megőrzési időszakát. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

* [Diagnosztikai naplók engedélyezése Azure Data Factory](./monitor-using-azure-monitor.md)

* [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a Azure Data Factory diagnosztikai beállításainak engedélyezése és naplók elküldése egy log Analytics-munkaterületre. A Log Analytics segítségével elemezheti és figyelheti a naplókat a rendellenes működéshez, és rendszeresen áttekintheti az eredményeket. Győződjön meg arról, hogy a Azure Data Factory üzemelő példányokhoz kapcsolódó adattárakhoz is engedélyezi a diagnosztikai beállításokat. A diagnosztikai beállítások engedélyezésével kapcsolatos útmutatásért tekintse meg az egyes szolgáltatások biztonsági alapterveit.

Ha a Integration Runtime egy Azure-beli virtuális gépen (VM) futtatja, akkor a virtuális gép diagnosztikai beállításait is engedélyeznie kell.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

* [Log Analytics séma](./monitor-using-azure-monitor.md#schema-of-logs-and-events)

* [Adatok gyűjtése Azure-beli virtuális gépekről Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Data Factory támogatott metrikáinak riasztásait a Azure monitor riasztások &amp; metrikák szakaszában tekintheti meg.

Azure Data Factory diagnosztikai beállításainak konfigurálása és naplók elküldése egy Log Analytics munkaterületre. A Log Analytics munkaterületen beállíthatja, hogy a riasztások az előre definiált feltételek betartása esetén kerüljenek érvénybe. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

Továbbá győződjön meg arról, hogy engedélyezte az adattárakhoz kapcsolódó szolgáltatások diagnosztikai beállításait. Az egyes szolgáltatások biztonsági alapterveit a következő témakörben találja: útmutatás.

* [Riasztások Azure Data Factory](./monitor-visually.md#alerts)

* [Az összes támogatott metrika lap](../azure-monitor/platform/metrics-supported.md)

* [Riasztások konfigurálása Log Analytics munkaterületen](../azure-monitor/platform/alerts-log.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális gépen futtatja, akkor használhatja az Azure-hoz készült Microsoft antimalware-t Cloud Services és Virtual Machines és konfigurálhatja a virtuális gépeket, hogy naplózza az eseményeket egy Azure Storage-fiókba. Konfiguráljon egy Log Analytics munkaterületet az események betöltéséhez a Storage-fiókokból, és szükség esetén hozzon létre riasztásokat. Kövesse az Azure Security Center: "számítási alkalmazások" című témakörben foglalt javaslatokat &amp; .

* [A Microsoft antimalware konfigurálása Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

* [A Virtual Machines vendég szintű figyelésének engedélyezése](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Azure Data Factory nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális GÉPEN (VM) futtatja, akkor engedélyezheti a parancssori naplózás naplózását. A Azure Security Center az Azure-beli virtuális gépek biztonsági eseménynaplóinak figyelését teszi lehetővé. Security Center kiépíti a Microsoft monitoring agentet az összes támogatott Azure-beli virtuális gépre, valamint az automatikus kiépítés engedélyezése esetén létrehozott újakra, vagy manuálisan is telepítheti az ügynököt. Az ügynök engedélyezi a 4688-es folyamat-létrehozási eseményt és a 4688-es eseményen belüli parancssori mezőt. A virtuális gépen létrehozott új folyamatokat az eseménynapló rögzíti, és a Security Center észlelési szolgáltatásai figyelik.

* [Adatgyűjtés az Azure Security Centerben](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Data Factory belül győződjön meg róla, hogy rendszeresen nyomon követheti és egyeztetheti a felhasználói hozzáférést. Data Factory-példányok létrehozásához az Azure-ba történő bejelentkezéshez használt felhasználói fióknak a közreműködő vagy tulajdonos szerepkör tagjának, vagy az Azure-előfizetés rendszergazdájának kell lennie.

Emellett a bérlői szinten a Azure Active Directory (AD) beépített szerepkörei vannak, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre olyan fiókok felderítéséhez, amelyek rendszergazdai hozzáféréssel rendelkeznek a Azure Data Factory példányok vezérlési síkjával.

Míg az Azure AD az ajánlott módszer a felhasználói hozzáférések adminisztrálása során, vegye figyelembe, hogy ha Integration Runtimet futtat egy Azure-beli virtuális gépen (VM), akkor a virtuális gép helyi fiókkal is rendelkezhet. A helyi és a tartományi fiókokat egyaránt felül kell vizsgálni és felügyelni kell, általában minimális helyigénysel. Emellett azt is javasoljuk, hogy a Privileged Identity Managert az időpontra vonatkozó szolgáltatásban tekintse át a rendszergazdai jogosultságok rendelkezésre állásának csökkentése érdekében.

* [Szerepkörök és engedélyek az Azure Data Factoryhoz](./concepts-roles-permissions.md)

* [Információk a Privileged Identity Managerről](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

* [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

* [Helyi fiókok adatai](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Azure Data Factory Azure Active Directory (ad) használatával biztosítja a hozzáférést a Azure Portalhoz és a Azure Data Factory-konzolhoz. Az Azure AD nem rendelkezik az alapértelmezett jelszavakkal, de az egyéni vagy külső alkalmazások alapértelmezett jelszavainak módosítása vagy nem engedélyezett.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos működési eljárások létrehozása dedikált rendszergazdai fiókok használatával az Azure Control Plant (Azure Portal) és a Azure Data Factory-konzol eléréséhez. A Azure Security Center identitás-és hozzáférés-kezelési szolgáltatással figyelheti az Azure AD-n belüli rendszergazdai fiókok számát.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ha a Integration Runtime Azure-beli virtuális gépen futtatja, az Virtual Machines Azure-beli rendszergazdai fiókok az Azure Privileged Identity Manager (PIM) használatával is konfigurálhatók. Az Azure Privileged Identity Manager számos lehetőséget kínál, például igény szerinti jogosultságszint-emelést, Multi-Factor Authentication és delegálási lehetőségeket, hogy az engedélyek csak adott időkeretek számára legyenek elérhetők, és egy második személyt is jóvá kell hagynia.

* [Azure Security Center identitás és hozzáférés ismertetése](../security-center/security-center-identity-access.md)

* [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

* [Információk a Privileged Identity Managerről](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Szerepkörök és engedélyek az Azure Data Factoryhoz](./concepts-roles-permissions.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az Azure-alkalmazás regisztrálása (egyszerű szolgáltatásnév) használatával lekérheti a tokent, amelyet az alkalmazás vagy a függvény használhat a Recovery Services-tárolók eléréséhez és használatához.

* [Az Azure REST API-k meghívása](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Az ügyfélalkalmazás (egyszerű szolgáltatásnév) regisztrálása az Azure AD-vel](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure Recovery Services API-információk](/rest/api/recoveryservices/)

* [A Azure Data Factory REST API vonatkozó információk](/rest/api/datafactory/)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése.

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bevezetésére és konfigurálására konfigurált, multi-Factor Authentication (MFA) rendszerjogosultságú hozzáférési munkaállomások használata.

* [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

Ha a Integration Runtime Azure-beli virtuális gépen (VM) futtatja, akkor a virtuális gépet emellett az Azure Sentinelbe is felhasználhatja. A Microsoft Azure Sentinel egy skálázható, Felhőbeli natív, biztonsági információkkal kapcsolatos esemény-felügyeleti (SIEM) és biztonsági előkészítési (felszárnyaló) megoldás. Az Azure Sentinel intelligens biztonsági elemzési és fenyegetésekkel kapcsolatos intelligenciát biztosít a vállalaton belül, így egyetlen megoldást kínál a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra és a fenyegetésekre való reagálásra.

* [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

* [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

* [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

* [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: az adatok előállítója felügyelt identitással társítható az Azure-erőforrások számára, amelyek az adott adatelőállítót jelölik. Ezt a felügyelt identitást Azure SQL Database hitelesítéshez használhatja. A kijelölt gyár ezen identitás használatával férhet hozzá az adatbázishoz, és másolhatja azokat az adatbázisból.

Ha a Integration Runtime (IR) egy Azure-beli virtuális gépen futtatja, akkor a felügyelt identitások használatával bármely olyan szolgáltatás hitelesíthető, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül. A virtuális gépen futó kód a felügyelt identitás használatával kérhet hozzáférési jogkivonatokat az Azure AD-hitelesítést támogató szolgáltatásokhoz.

* [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Mik azok az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md)

* [Azure SQL Database adatmásolása és átalakítása a Azure Data Factory használatával](./connector-azure-sql-database.md)

* [Azure Active Directory hitelesítés konfigurálása és kezelése Azure SQL Database használatával](../azure-sql/database/authentication-aad-configure.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

Ha egy Azure-beli virtuális gépen futtatja a futtatókörnyezet-integrációt, akkor ellenőriznie kell a helyi biztonsági csoportokat és a felhasználókat, hogy ne legyenek olyan váratlan fiókok, amelyek veszélyeztethetik a rendszert.

* [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

* [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

* [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (ad) bejelentkezési tevékenységhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását. Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics belül is konfigurálhatja a kívánt naplózási riasztásokat.

Ha a Integration Runtimet egy Azure-beli virtuális gépen (VM) futtatja, akkor az Azure Sentinel-be a virtuális gépre. A Microsoft Azure Sentinel egy skálázható, Felhőbeli natív, biztonsági információkkal kapcsolatos esemény-felügyeleti (SIEM) és biztonsági előkészítési (felszárnyaló) megoldás. Az Azure Sentinel intelligens biztonsági elemzési és fenyegetésekkel kapcsolatos intelligenciát biztosít a vállalaton belül, így egyetlen megoldást kínál a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra és a fenyegetésekre való reagálásra.

* [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával](../event-hubs/authorize-access-azure-active-directory.md)

* [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerként Azure Data Factory erőforrásokhoz, például Azure SQL Databasehoz vagy az Azure Virtual Machineshoz. A vezérlési síkon (a Azure Portal) való bejelentkezéshez használja a Azure AD Identity Protection és a kockázati észlelési funkciókat a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszok konfigurálásához. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

* [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

* [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [Azure Active Directory hitelesítés konfigurálása és kezelése SQL-sel](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)

* [Azure Active Directory-hitelesítés engedélyezése az Azure-SSIS integrációs modulhoz](./enable-aad-authentication-azure-ssis-ir.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: azon forgatókönyvek esetében, amelyekben a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez, az Azure Ügyfélszéf egy felületet biztosít az ügyfelek számára az ügyféladatok hozzáférési kérelmeinek áttekintéséhez és jóváhagyásához vagy elutasításához. Vegye figyelembe, hogy habár az Azure kulcstároló nem érhető el a Azure Data Factoryhoz, az Azure kulcstároló támogatja a Azure SQL Database és az Azure Virtual Machinest.

* [A Ügyfélszéf megismerése](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

Használja a Azure SQL Database adatfelderítési és besorolási funkciót. Az adatfelderítés és-besorolás olyan speciális képességeket biztosít, amelyek a Azure SQL Databaseba vannak építve a &amp; bizalmas adatoknak az adatbázisokban való felderítéséhez, besorolásához, címkézéséhez.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Adatfelderítés és besorolás használata az Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az integrációs modulokat a virtuális hálózat (VNet)/subnet és megfelelő címkével kell elválasztani.

 A hálózati elkülönítés végrehajtásához privát végpontokat is használhat. Az Azure Private-végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így lényegében bekapcsolja a szolgáltatást a virtuális hálózatba.

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Privát hivatkozás ismertetése](../private-link/private-endpoint-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: adatforrások (például Azure SQL Database) számára a Azure Data Factory központi telepítés bizalmas adatainak tárolása és feldolgozása, a kapcsolódó erőforrások bizalmasként való megjelölése címkék használatával.

Ha a privát hivatkozás elérhető, használjon privát végpontokat az Azure Data Factory-folyamathoz kapcsolódó erőforrások biztonságossá tételéhez. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Emellett csökkentheti az adatkiszűrése kockázatát azáltal, hogy a kimenő szabályok szigorú készletét konfigurálja egy hálózati biztonsági csoportra (NSG), és társítja az adott NSG az alhálózattal.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Biztonsági konfigurációval rendelkező NSG létrehozása](../virtual-network/tutorial-filter-network-traffic.md)

* [Az Azure privát hivatkozásának megismerése](../private-link/private-link-overview.md)

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: Ha a felhőalapú adattár támogatja a HTTPS-t vagy a TLS-t, az adatátviteli Data Factory szolgáltatások és a felhőalapú adattárolók közötti adatforgalom a biztonságos csatorna HTTPS vagy TLS protokollon keresztül történik. A használt TLS-verzió 1,2.

Az Azure SQL Database és az Azure szinapszis Analytics szolgáltatással létesített összes kapcsolat titkosítást (SSL/TLS) igényel, miközben az adatok átvitele az adatbázisba és az adatbázisból történik. Amikor JSON használatával készít folyamatokat, adja hozzá a titkosítási tulajdonságot, és állítsa igaz értékre a kapcsolódási karakterláncban. Az Azure Storage esetében a kapcsolati sztringben HTTPS protokollt használhat.

* [A Azure Data Factory-átvitel titkosításának ismertetése](./data-movement-security-considerations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: ha a Azure SQL Database példányok másolásához és átalakításához Azure Data Factory használ, használja a Azure SQL Database adatfelderítési és besorolási funkciót. Az adatfelderítés és-besorolás olyan speciális képességeket biztosít, amelyek a Azure SQL Databaseba vannak építve a &amp; bizalmas adatoknak az adatbázisokban való felderítéséhez, besorolásához, címkézéséhez.

Az adatfelderítési és-besorolási funkciók még nem érhetők el más Azure-szolgáltatásokhoz.

* [Adatfelderítés és besorolás használata az Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozhatja a Azure Data Factory vezérlő síkja (a Azure Portal) elérését.

Data Factory-példányok létrehozásához az Azure-ba történő bejelentkezéshez használt felhasználói fióknak a közreműködő vagy tulajdonos szerepkör tagjának, vagy az Azure-előfizetés rendszergazdájának kell lennie.

Az Data Factory adatforrások, például a Azure SQL Database esetében az Azure RBAC kapcsolatos további információkért tekintse meg az adott szolgáltatás biztonsági alaptervét.

* [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

* [Szerepkörök és engedélyek az Azure Data Factoryhoz](./concepts-roles-permissions.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: javasoljuk, hogy engedélyezze az adattitkosítási mechanizmust az Azure Data Factory-környezetekhez kapcsolódó adattárakhoz. Az inaktív adatok titkosításával kapcsolatos további információkért tekintse meg az adott szolgáltatás biztonsági alaptervét.

Ha a Integration Runtimet egy Azure-beli virtuális gépen futtatja, a Windows Virtual Machines (VM) virtuális lemezeit a kiszolgálóoldali titkosítás vagy az Azure Disk Encryption (ADE) használatával titkosítja a rendszer. Azure Disk Encryption a Windows BitLocker szolgáltatásával titkosítja a felügyelt lemezeket az ügyfél által felügyelt kulcsokkal a vendég virtuális gépen belül. Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t, mivel lehetővé teszi a virtuális gépekhez tartozó operációsrendszer-típusok és-lemezképek használatát a Storage szolgáltatásban tárolt adattitkosítással.

A hitelesítő adatokat vagy a titkos értékeket egy Azure Key Vault tárolhatja, és a folyamat végrehajtása során felhasználhatja a tevékenységeket. Az adattárakhoz és a számítási feladatokhoz tartozó hitelesítő adatokat egy Azure Key Vault is tárolhatja. Azure Data Factory beolvassa a hitelesítő adatokat, amikor egy adattárat/számítást használó tevékenységet hajt végre.

* [A inaktív adatok titkosításának megértése Azure Data Factory](./data-movement-security-considerations.md)

* [Az Azure Managed Disks kiszolgálóoldali titkosítása](../virtual-machines/disk-encryption.md)

* [Azure Disk Encryption Windows rendszerű virtuális gépekhez](../virtual-machines/windows/disk-encryption-overview.md)

* [Azure Key Vault titkok használata a folyamat tevékenységeiben](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Hitelesítő adatok a Azure Key Vaultban](./store-credentials-in-key-vault.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások Azure Data Factory és kapcsolódó erőforrásokra vonatkoznak.

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/platform/alerts-activity-log.md)

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/platform/alerts-activity-log.md)

* [Azure Storage Analytics-naplózás](../storage/common/storage-analytics-logging.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: Ha adattárakként Azure SQL Database használ, engedélyezze a speciális adatbiztonságot a Azure SQL Database és a Azure Security Center ajánlásainak követését az Azure SQL Server-kiszolgálókon a sebezhetőségi felmérések végrehajtásához.

Ha a Integration Runtimet egy Azure-beli virtuális gépen (VM) futtatja, kövesse Azure Security Center a sebezhetőségi felmérések a virtuális gépeken való végrehajtásával kapcsolatos ajánlásokat. A virtuális gépek sebezhetőségi felmérésének elvégzéséhez használja az Azure Security által ajánlott vagy harmadik féltől származó megoldást.

* [Sebezhetőségi felmérések futtatása a Azure SQL Databaseon](../azure-sql/database/sql-vulnerability-assessment.md)

* [A speciális adatbiztonság engedélyezése](../azure-sql/database/azure-defender-for-sql.md)

* [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális GÉPEN (VM) futtatja, az Azure Update Management megoldással kezelheti a virtuális gépek frissítéseit és javításait. A Update Management a helyileg konfigurált frissítési tárházra támaszkodik a támogatott Windows-rendszerek javításához. Az olyan eszközök, mint a System Center Updates Publisher (a frissítések közzétevője) lehetővé teszik egyéni frissítések közzétételét Windows Server Update Services (WSUS) szolgáltatásban. Ez a forgatókönyv lehetővé teszi, hogy Update Management a harmadik féltől származó szoftverrel rendelkező frissítési adattárként Configuration Manager használó gépeket.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Update Management megoldás az Azure-ban](../automation/update-management/overview.md)

* [Azure-beli virtuális gépek frissítéseinek és javításának kezelése](../automation/update-management/manage-updates-for-vm.md)

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális GÉPEN (VM) futtatja, akkor külső gyártótól származó javítási felügyeleti megoldást is használhat. Az Azure Update Management megoldással kezelheti a virtuális gépek frissítéseit és javításait. A Update Management a helyileg konfigurált frissítési tárházra támaszkodik a támogatott Windows-rendszerek javításához. Az olyan eszközök, mint a System Center Updates Publisher (a frissítések közzétevője) lehetővé teszik egyéni frissítések közzétételét Windows Server Update Services (WSUS) szolgáltatásban. Ez a forgatókönyv lehetővé teszi, hogy Update Management a harmadik féltől származó szoftverrel rendelkező frissítési adattárként Configuration Manager használó gépeket.

* [Update Management megoldás az Azure-ban](../automation/update-management/overview.md)

* [Azure-beli virtuális gépek frissítéseinek és javításának kezelése](../automation/update-management/manage-updates-for-vm.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális gépen futtatja, akkor a vizsgálat eredményeit konzisztens időközönként exportálja, és összehasonlítja az eredményeket annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. Ha a Azure Security Center által javasolt sebezhetőségi kezelési javaslatot használja, a kiválasztott megoldás portálján megtekintheti a korábbi vizsgálati adataikat.

* [A virtuális gépek integrált sebezhetőségi ellenőrzőeszközének megismerése](../security-center/deploy-vulnerability-assessment-vm.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális gépen futtatja, használhatja a natív sebezhetőségi képolvasót. A Azure Security Center által tartalmazott sebezhetőségi képolvasót a Qualys működteti. A Qualys képolvasó a vezető eszköz a biztonsági rések valós idejű azonosításához az Azure-Virtual Machines.

Ha Security Center észleli a biztonsági réseket, a megállapításokat és a kapcsolódó információkat a javaslatok alapján mutatja be. A kapcsolódó információk közé tartozik a Szervizelési lépések, a kapcsolódó CVEs, a CVSS pontszámok és egyebek. Megtekintheti egy vagy több előfizetés vagy egy adott virtuális gép azonosított biztonsági réseit.

* [A virtuális gépekhez készült integrált sebezhetőségi képolvasó](../security-center/deploy-vulnerability-assessment-vm.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

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

**Útmutató**: az Azure-erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézést, a felügyeleti csoportokat és a különálló előfizetéseket, ahol szükséges. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: jóváhagyott Azure-erőforrások és jóváhagyott szoftverek definiálása számítási erőforrásokhoz.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára.

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális GÉPEN (VM) futtatja, az Azure-beli virtuális gépek leltározásával automatizálhatja a Virtual Machines összes szoftverével kapcsolatos információk gyűjtését. Az Azure Automation teljes körű felügyeletet biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során.

Megjegyzés: a szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A telepítés dátumának és egyéb információinak eléréséhez az ügyfélnek a vendég szintű diagnosztika engedélyezéséhez és a Windows-eseménynaplók Log Analytics munkaterületre való bekapcsolásához van szükség.

* [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

* [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális gépen futtatja, a Azure Automation teljes körű ellenőrzést biztosít az üzembe helyezés, a műveletek és a számítási feladatok és erőforrások leszerelése során. Change Tracking használatával azonosíthatja a Virtual Machines telepített összes szoftvert. A jogosulatlan szoftverek eltávolításához saját folyamatot alkalmazhat, vagy használhatja Azure Automation állapot konfigurációját.

* [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

* [A környezet változásainak követése a Change Tracking megoldással](../automation/change-tracking/overview.md)

* [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális GÉPEN (VM) futtatja, akkor a Azure Security Center adaptív alkalmazás-vezérlőelemekkel biztosíthatja, hogy csak a hitelesített szoftverek fussanak, és az összes jogosulatlan szoftver le legyen tiltva a virtuális gépeken.

* [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: az adaptív alkalmazás-vezérlés intelligens, automatizált, teljes körű megoldás a Azure Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban és a nem Azure-beli gépeken (Windows és Linux). Egy harmadik féltől származó megoldás implementálása, ha az nem felel meg a szervezet követelményeinek.

Vegye figyelembe, hogy ez csak akkor érvényes, ha a Integration Runtime Azure-beli virtuális gépen fut.

* [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

* [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: Ha egy Azure-beli virtuális gépen futtatja a futtatókörnyezet-integrációt, a parancsfájlok típusától függően az operációs rendszerre jellemző konfigurációk vagy harmadik féltől származó erőforrások segítségével korlátozhatja, hogy a felhasználók képesek legyenek parancsfájlokat végrehajtani az Azure számítási erőforrásokon belül. Emellett kihasználhatja Azure Security Center adaptív alkalmazás vezérlőelemeit is, hogy csak a hitelesített szoftverek fussanak, és az összes jogosulatlan szoftver le legyen tiltva az Azure Virtual Machines.

* [A PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](/powershell/module/microsoft.powershell.security/set-executionpolicy)

* [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az Azure-környezetben üzembe helyezett magas kockázatú alkalmazások elkülöníthetők a virtuális hálózatok, az alhálózatok, az előfizetések, a felügyeleti csoportok stb. számára, és megfelelően biztonságosak a Azure Firewall, a webalkalmazási TŰZFAL (WAF) vagy a hálózati biztonsági csoport (NSG) használatával.

* [Virtuális hálózatok és virtuális gépek az Azure-ban](../virtual-machines/network-overview.md)

* [Mi az Azure Firewall?](../firewall/overview.md)

* [Mi az az Azure Web Application Firewall?](../web-application-firewall/overview.md)

* [Hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md)

* [Mi az Azure Virtual Network?](../virtual-network/virtual-networks-overview.md)

* [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)

* [Útmutató az előfizetéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Policy Azure Data Factory szabványos biztonsági konfigurációinak meghatározása és implementálása. Használjon Azure Policy aliasokat a "Microsoft. DataFactory" névtérben egyéni szabályzatok létrehozásához a Azure Data Factory példányok konfigurációjának naplózásához vagy érvényesítéséhez.

* [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: Ha egy Azure-beli virtuális gépen futtatja a futtatókörnyezet-integrációt, használja a Azure Security Center javaslatot [a Virtual Machines biztonsági konfigurációinak javításai című témakörben] az összes számítási erőforrás biztonsági beállításainak fenntartásához.

* [Azure Security Center javaslatok figyelése](../security-center/security-center-recommendations.md)

* [Azure Security Center javaslatok szervizelése](../security-center/security-center-remediate-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

* [Azure Resource Manager sablonok létrehozásával kapcsolatos információk](../virtual-machines/windows/ps-template.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: ha a Integration Runtimet egy Azure-beli virtuális GÉPEN (VM) futtatja, vegye figyelembe, hogy több lehetőség áll rendelkezésre a virtuális gépek biztonságos konfigurációjának karbantartására az üzembe helyezéshez:
- Azure Resource Manager sablonok: ezek olyan JSON-alapú fájlok, amelyek a virtuális gépek Azure Portalból való üzembe helyezéséhez szükségesek, és az egyéni sablont is karban kell tartani. A Microsoft végzi el a karbantartást az alapsablonokon.
- Egyéni virtuális merevlemez (VHD): bizonyos esetekben előfordulhat, hogy olyan egyéni VHD-fájlokat kell használnia, mint például a más módon nem felügyelhető összetett környezetek kezelése. -Azure Automation állapot konfigurálása: miután telepítette az alaprendszert, ez a beállítások részletesebb vezérléséhez használható, és az Automation-keretrendszer segítségével kényszeríthető ki.

A legtöbb esetben a Microsoft Base VM-sablonok a Azure Automation kívánt állapot-konfigurációval együtt segíthetnek a biztonsági követelmények teljesítésében és fenntartásában.

* [A virtuálisgép-sablon letöltésével kapcsolatos információk](/previous-versions/azure/virtual-machines/windows/download-template)

* [Azure Resource Manager sablonok létrehozásával kapcsolatos információk](../virtual-machines/windows/ps-template.md)

* [Egyéni virtuális merevlemez feltöltése az Azure-ba](/azure-stack/operator/azure-stack-add-vm-image)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

* [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Az Azure Repos dokumentációja](/azure/devops/repos/index)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: Ha egyéni lemezképeket használ, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez. A tárolói lemezképek esetében tárolja azokat Azure Container Registryban, és használja ki az Azure RBAC annak biztosítására, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez.

A Data Factory közreműködő szerepkör az adat-előállítók létrehozásához és kezeléséhez, valamint a bennük található alárendelt erőforrásokhoz használható.

* [Az Azure RBAC ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Az Azure-RBAC megismerése Container Registry](../container-registry/container-registry-roles.md)

* [Az Azure RBAC konfigurálása](../role-based-access-control/quickstart-assign-role-user-portal.md)

* [Szerepkörök és engedélyek az Azure Data Factoryhoz](./concepts-roles-permissions.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a "Microsoft. DataFactory" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: Ez a javaslat akkor alkalmazható, ha a Integration Runtime Azure-beli virtuális gépen fut. Azure Automation állapot-konfiguráció a kívánt állapot-konfigurációs (DSC) csomópontok egyike a felhőben vagy a helyszíni adatközpontban. Lehetővé teszi, hogy gyorsan és könnyen, egy központi, biztonságos helyen lehessen méretezni a több ezer gépen. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják.

* [Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](../automation/automation-dsc-onboarding.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a "Microsoft. DataFactory" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Az Azure-erőforrások konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés, ha nem létezik] lehetőséget.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: Ez a javaslat akkor alkalmazható, ha a Integration Runtime Azure-beli virtuális gépen fut. Azure Automation állapot-konfiguráció a kívánt állapot-konfigurációs (DSC) csomópontok egyike a felhőben vagy a helyszíni adatközpontban. Lehetővé teszi, hogy gyorsan és könnyen, egy központi, biztonságos helyen lehessen méretezni a több ezer gépen. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják.

* [Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](../automation/automation-dsc-onboarding.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében.

A hitelesítő adatokat és a titkos értékeket egy Azure Key Vault is tárolhatja, és a folyamat végrehajtása során felhasználhatja őket a tevékenységek továbbításához. Győződjön meg arról, hogy a helyreállítható törlés engedélyezve van.

* [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Key Vault létrehozása](../key-vault/secrets/quick-create-portal.md)

* [Hitelesítés Key Vault](../key-vault/general/authentication.md)

* [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

* [Azure Key Vault-beli titkos kulcsok használata a folyamattevékenységekben](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Törlés Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az adatok előállítójának létrehozásakor a rendszer felügyelt identitást hozhat létre a gyári létrehozással együtt. A felügyelt identitás egy Azure Active Directory számára regisztrált felügyelt alkalmazás, amely az adott adatelőállítót jelöli.

* [Azure Data Factory felügyelt identitása](./data-factory-service-identity.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

* [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális gépen futtatja, akkor a Microsoft antimalware for Azure Windows Virtual Machines segítségével folyamatosan figyelheti és védheti az erőforrásait.

* [A Microsoft antimalware konfigurálása Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például Azure app Service), de nem fut a tartalomon.

A nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage stb.

A Storage-fiókokba feltöltött kártevők észleléséhez használja a Azure Security Center veszélyforrások észlelését az adatszolgáltatásokhoz.

* [Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

* [Az adatszolgáltatások fenyegetés-észlelésének megismerése Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: telepítésekor az Azure-hoz készült Microsoft antimalware szolgáltatás alapértelmezés szerint automatikusan telepíti a legújabb aláírást, platformot és motor-frissítéseket. Kövesse az Azure Security Center: "számítási alkalmazások" című témakörben található javaslatokat &amp; , hogy az összes végpont naprakész legyen a legújabb aláírásokkal. A Windows operációs rendszer további biztonsággal biztosítható, hogy a Microsoft Defender komplex veszélyforrások elleni védelmi szolgáltatásával, amely integrálható a Azure Security Centersal, a vírus-vagy kártevő-alapú támadások kockázatát korlátozza.

* [A Microsoft antimalware telepítése Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális GÉPEN (VM) futtatja, engedélyezze Azure Backup és konfigurálja a virtuális gépet, valamint az automatikus biztonsági mentések kívánt gyakoriságát és megőrzési idejét.

Az egyes adattárak esetében a szolgáltatás biztonsági alapkonfigurációját a rendszeres, automatizált biztonsági mentések végrehajtásával kapcsolatos javaslatokért lásd.

* [Az Azure virtuális gépek biztonsági mentésének áttekintése](../backup/backup-azure-vms-introduction.md)

* [Azure-beli virtuális gép biztonsági mentése a virtuális gép beállításaiból](../backup/backup-azure-vms-first-look-arm.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális GÉPEN (VM) futtatja, engedélyezze a Azure Backup és a célként megadott Azure-beli virtuális gépeket, valamint a kívánt gyakoriságot és megőrzési időt. Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül.

Az egyes adattárak esetében a szolgáltatás biztonsági alapkonfigurációját a rendszeres, automatizált biztonsági mentések végrehajtásával kapcsolatos javaslatokért lásd.

* [Az Azure virtuális gépek biztonsági mentésének áttekintése](../backup/backup-azure-vms-introduction.md)

* [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: ha a Integration Runtime egy Azure-beli virtuális gépen futtatja, akkor a Azure Backupon belül időnként végezheti el a tartalom adatvisszaállításának lehetőségét. Ha szükséges, tesztelje a visszaállítási tartalmat egy elkülönített VLAN-ra. Rendszeresen tesztelheti az ügyfél által felügyelt kulcsok biztonsági mentését.

Az egyes adattárak esetében a biztonsági mentések érvényesítésére vonatkozó útmutatásért tekintse meg a szolgáltatás biztonsági alaptervét.

* [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](../backup/backup-azure-restore-files-from-vm.md)

* [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: ha a Integration Runtimet egy Azure-beli virtuális GÉPEN (VM) futtatja, és a virtuális gépet a Azure Backup használatával visszaállítjuk, a virtuális gép Storage Service encryption (SSE) használatával titkosítva van. A Azure Backup a Azure Disk Encryption használatával titkosított Azure-beli virtuális gépek biztonsági mentését is elvégezheti. A Azure Disk Encryption a BitLocker titkosítási kulcsaival (BEKs) integrálható, amelyek titkos kulccsal rendelkeznek a Key vaultban. A Azure Disk Encryption Azure Key Vault kulcs-titkosítási kulcsokkal (KEK) is integrálva van. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához engedélyezze a Key Vault Soft-Delete.

* [Soft Delete a virtuális gépekhez](../backup/backup-azure-security-feature-cloud.md)

* [Azure Key Vault: a helyreállítható törlés áttekintése](../key-vault/general/soft-delete-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

* [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

* [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

* [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

* [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Használhatja a Azure Security Center adatösszekötőt a riasztások Sentinel továbbításához.

* [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

* [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

* [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: 

* [Kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [További információt a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos, a Microsoft által felügyelt felhő-infrastruktúra,-szolgáltatások és-alkalmazások fejlesztésére vonatkozó stratégiáról és végrehajtásáról itt talál.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)