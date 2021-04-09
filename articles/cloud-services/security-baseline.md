---
title: Azure biztonsági alapkonfiguráció az Azure Cloud Services
description: Az Azure Cloud Services biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c389cfd2af6fe83ec232e5f205041676ba46bd2a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558193"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Azure biztonsági alapkonfiguráció az Azure Cloud Services

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Microsoft Azure Cloud Servicesre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Cloud Services vonatkozó kapcsolódó útmutatás. A Cloud Services nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Cloud Services teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Cloud Services biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: hozzon létre egy klasszikus Azure-Virtual Network külön nyilvános és magánhálózati alhálózattal, hogy kikényszerítse a megbízható portok és IP-címtartományok alapján történő elkülönítést. Ezeknek a virtuális hálózatoknak és alhálózatoknak a klasszikus Virtual Network (klasszikus üzembe helyezési) alapú erőforrásoknak kell lenniük, és nem a jelenlegi Azure Resource Manager erőforrásai.  

Engedélyezheti vagy megtagadhatja a forgalmat egy hálózati biztonsági csoporttal, amely a hozzáférés-vezérlési szabályokat tartalmazza a forgalmi irány, a protokoll, a forrás címe és a port, valamint a célcím és a port alapján. A hálózati biztonsági csoport szabályai bármikor megváltoztathatók, és a rendszer minden társított példányra alkalmazza a módosításokat.

Microsoft Azure Cloud Services (klasszikus) nem helyezhető el Azure Resource Manager virtuális hálózatokban. A Resource Manager-alapú virtuális hálózatok és a klasszikus üzembe helyezési alapú virtuális hálózatok azonban a peering használatával csatlakoztathatók. 

- [Hálózati biztonsági csoport áttekintése](../virtual-network/network-security-groups-overview.md)

- [Társviszony létesítése virtuális hálózatok között](./cloud-services-connectivity-and-networking-faq.md#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure Cloud Services konfigurációjának dokumentálása és a változások figyelése. A szolgáltatás konfigurációs fájljával meghatározhatja, hogy hány szerepkört kell telepíteni a szolgáltatás egyes szerepköreihez, a konfigurációs beállítások értékeit, valamint a szerepkörhöz társított tanúsítványok ujjlenyomatai megfelelnek. 

Ha a szolgáltatás egy virtuális hálózat része, a hálózat konfigurációs adatait meg kell adni a szolgáltatás konfigurációs fájljában, valamint a virtuális hálózat konfigurációs fájljában. A szolgáltatás konfigurációs fájljának alapértelmezett kiterjesztése a. cscfg. Vegye figyelembe, hogy a konfiguráció kényszerítéséhez a Azure Policy nem támogatott a klasszikus központi telepítések esetén.

Állítsa be a Cloud Service konfigurációs értékeit a szolgáltatás konfigurációs fájljába (. cscfg) és a definícióját egy Service Definition (. csdef) fájlba. A szolgáltatás-definíciós fájllal definiálhatja az alkalmazás szolgáltatási modelljét. Adja meg a felhőalapú szolgáltatás számára elérhető szerepköröket, valamint a szolgáltatási végpontokat is. Az Azure Cloud Services konfigurációjának naplózása a szolgáltatás konfigurációs fájljával. Minden újrakonfigurálás a ServiceConfig. cscfg fájllal végezhető el. 

Figyelje a nem kötelező NetworkTrafficRules-szolgáltatás definícióját, amely korlátozza, hogy mely szerepkörök kommunikálhatnak a megadott belső végpontokkal. Konfigurálja a NetworkTrafficRules csomópontot, amely egy opcionális elem a szolgáltatás definíciós fájljában annak megadásához, hogy a szerepkörök hogyan kommunikáljanak egymással. Korlátozza, hogy mely szerepkörök férhetnek hozzá az adott szerepkör belső végpontjaihoz.  Vegye figyelembe, hogy a szolgáltatás definíciója nem módosítható. 

Engedélyezze a hálózati biztonsági csoport adatfolyam-naplóit, és küldje el a naplókat egy Azure Storage-fiókba a naplózáshoz. Elküldheti a folyamat naplóit egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-bérlő forgalmi szokásaiba. A Traffic Analytics néhány előnye, hogy képes megjeleníteni a hálózati tevékenységeket, azonosíthatja a gyakori helyeket és a biztonsági fenyegetéseket, megismerheti a forgalmi folyamatok mintáit, és meghatározhatja a hálózati helytelen konfigurációkat.

- [Azure Resource Manager vs. klasszikus üzembe helyezés – az üzembe helyezési modellek és az erőforrások állapotának ismertetése](../azure-resource-manager/management/deployment-models.md)

- [Cloud Services konfigurációs fájl](schema-cscfg-file.md)

- [Azure Policy által támogatott szolgáltatások listája](/cli/azure/azure-services-the-azure-cli-can-manage)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: a Microsoft a TRANSPORT Layer Security (TLS) 2.0-s protokoll használatával gondoskodik az adatvédelemről, amikor az Azure Cloud Services és az ügyfelek között utazik. A Microsoft-adatközpontok egy TLS-kapcsolatot egyeztetnek az Azure-szolgáltatásokhoz csatlakozó ügyfélszámítógépekkel. A TLS erős hitelesítést, az üzenetek védelmét és integritását (az üzenetek illetéktelen módosításának, elfogásának és hamisításának észlelését), az együttműködési képességet, az algoritmus rugalmasságát, valamint a könnyű üzembe helyezést és használatot biztosít.

- [Titkosítási alapok](../security/fundamentals/encryption-overview.md)

- [TLS/SSL-tanúsítványok konfigurálása](cloud-services-configure-ssl-certificate-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure Cloud egy többrétegű hálózati biztonságot valósít meg a platform szolgáltatásainak az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelme érdekében. A Azure DDoS Protection az Azure Cloud folyamatos figyelési folyamatának része, amely folyamatosan javul a penetrációs tesztelésen keresztül. Ez a DDoS Protection úgy lett kialakítva, hogy ne csak a külső támadásoktól, hanem más Azure-bérlőtől is ellenálljanak. 

Az Azure Cloud Services-on belül többféleképpen is blokkolhatja vagy megtagadhatja a kommunikációt a platform szintű védelem mellett. Ezek a következők: 

-  Indítási feladat létrehozása egy adott IP-cím szelektív letiltásához
-  Azure-beli webes szerepkörök hozzáférésének korlátozása megadott IP-címekhez az IIS-web.config fájljának módosításával

A bejövő adatforgalom megakadályozása a Cloud Services alapértelmezett URL-címére vagy nevére (például *. cloudapp.net). Állítsa a gazdagép fejlécét egy egyéni DNS-névre a hely kötésének konfigurálása területen a Cloud Services-definíciós (*. csdef) fájlban.

Megtagadási szabály konfigurálása a klasszikus előfizetés-rendszergazdai hozzárendelések esetében. Alapértelmezés szerint a belső végpontok meghatározása után a kommunikáció bármely szerepkörről a szerepkör belső végpontja számára korlátozás nélkül elvégezhető. A kommunikáció korlátozásához hozzá kell adnia egy NetworkTrafficRules elemet a ServiceDefinition elemhez a szolgáltatás definíciós fájljában.

- [Hogyan lehet letiltani vagy letiltani a bejövő forgalmat a felhőalapú szolgáltatás alapértelmezett URL-címére](./cloud-services-connectivity-and-networking-faq.md#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Azure DDOS Protection](./cloud-services-connectivity-and-networking-faq.md#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Adott IP-cím blokkolása](./cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: az Azure Network Watcher, a hálózati teljesítmény figyelése, a diagnosztika és az elemzési szolgáltatás használata, amely lehetővé teszi az Azure-hálózatok figyelését. A Network Watcher ügynök virtuálisgép-bővítményének követelménye az igény szerinti hálózati forgalom rögzítése, valamint az Azure Virtual Machines egyéb speciális funkciói. Telepítse a Network Watcher Agent virtuálisgép-bővítményt, és kapcsolja be a hálózati biztonsági csoport adatfolyam-naplóit.

A folyamat naplózásának konfigurálása hálózati biztonsági csoportban. Tekintse át, hogyan helyezheti üzembe a Network Watcher virtuálisgép-bővítményt egy meglévő, a klasszikus üzemi modellel telepített virtuális gépen.

- [A folyamat naplózásának konfigurálása hálózati biztonsági csoporton](../virtual-machines/extensions/network-watcher-linux.md)

- [A flow-naplók konfigurálásával kapcsolatos további információkért látogasson el ide:](/cli/azure/azure-services-the-azure-cli-can-manage)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Cloud Services nem rendelkezik beépített azonosítókkal vagy IP-címekkel. Az ügyfelek a szervezeti követelmények alapján választhatják ki és helyezhetik üzembe a további hálózati AZONOSÍTÓkat vagy IP-címeket az Azure Marketplace-en. Harmadik féltől származó megoldások használatakor ügyeljen arra, hogy alaposan tesztelje a kiválasztott AZONOSÍTÓkat vagy IP-címeket az Azure Cloud Services a megfelelő működés és működés biztosítása érdekében.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: az Azure Cloud Serviceshoz csatolt szolgáltatási tanúsítványok, amelyek lehetővé teszik a szolgáltatáshoz való biztonságos kommunikációt. Ezek a tanúsítványok a szolgáltatások definíciójában vannak meghatározva, és automatikusan települnek a virtuális gépre, amely egy webes szerepkör példányát futtatja. Webes szerepkör esetében például használhat olyan szolgáltatás-tanúsítványt, amely képes hitelesíteni a kitett HTTPS-végpontokat. 

A tanúsítvány frissítéséhez csak új tanúsítványt kell feltöltenie, és módosítania kell az ujjlenyomat értékét a szolgáltatás konfigurációs fájljában.

Használja a TLS 1,2 protokollt, amely a leggyakrabban használt módszer az adatok titkosságának és integritásának biztosítására. 

Általánosságban elmondható, hogy a webalkalmazások védelméhez és a támadásokkal szembeni védelemhez (például a OWASP Top 10-hez) egy Azure-webalkalmazási tűzfalat támogató Azure-Application Gateway üzembe helyezhet a webalkalmazások védelméhez. 

- [Szolgáltatási tanúsítványok](cloud-services-certs-create.md)

- [TLS konfigurálása alkalmazáshoz az Azure-ban](cloud-services-configure-ssl-certificate-portal.md)

- [Application Gateway üzembe helyezése](../application-gateway/quick-create-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure Cloud Services konfigurációjának megerősítése és a módosítások figyelése. A szolgáltatás konfigurációs fájlja meghatározza a szolgáltatás egyes szerepköreihez telepítendő szerepkör-példányok számát, a konfigurációs beállítások értékeit, valamint a szerepkörhöz társított tanúsítványok ujjlenyomatai megfelelnek. 

Ha a szolgáltatás egy virtuális hálózat része, a hálózat konfigurációs adatait meg kell adni a szolgáltatás konfigurációs fájljában, valamint a virtuális hálózat konfigurációs fájljában. A szolgáltatás konfigurációs fájljának alapértelmezett kiterjesztése a. cscfg.

Vegye figyelembe, hogy Azure Policy nem támogatott az Azure Cloud Services a konfiguráció kényszerítéséhez.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: az Azure-beli hálózati biztonsági csoportokkal az azure-Virtual Network Azure-erőforrásaira irányuló és onnan érkező hálózati forgalom szűrhető. A hálózati biztonsági csoport olyan biztonsági szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják a bejövő hálózati forgalmat, illetve a kimenő hálózati forgalmat különböző típusú Azure-erőforrásokból. Az egyes szabályokhoz meghatározhatja a forrást és a célt, valamint a használni kívánt portot és protokollt.

Az egyes hálózati biztonsági csoportokra vonatkozó szabályok "Description" (Leírás) mezőjét használhatja az Azure Cloud Servicesban a szabályok dokumentálására, amelyek lehetővé teszik a hálózatra irányuló adatforgalmat.

- [Hálózati forgalom szűrése hálózati biztonsági csoport szabályaival](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure Traffic Manager beépített végpont-figyelési és automatikus végpont-feladatátvételi funkcióinak használata. Segítségükkel magas rendelkezésre állású alkalmazásokat biztosíthat, amelyek rugalmasan használhatók a végpontok és az Azure-régiók meghibásodása esetén. A végpontok figyelésének konfigurálásához meg kell adnia bizonyos beállításokat a Traffic Manager profiljában.

Betekintést nyerhet az Azure-ba, az előfizetési szintű eseményekbe. Ilyen információkat tartalmaz, mint amikor egy erőforrás módosítva van, vagy amikor a virtuális gép elindul. Tekintse meg a Azure Portal vagy a bejegyzéseket a PowerShell és a CLI használatával. 

Hozzon létre egy diagnosztikai beállítást, amely elküldi a műveletnapló Azure Monitor, Azure Event Hubs az Azure-on kívülre vagy az Azure Storage-ba archiválásra. Az értesítési riasztások Azure Monitor konfigurálása, ha az Azure-Cloud Services kritikus erőforrásai módosulnak. 

- [Azure-tevékenység naplója](../azure-monitor/essentials/activity-log.md)

- [Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../azure-monitor/alerts/alerts-activity-log.md)

- [Traffic Manager figyelés](../traffic-manager/traffic-manager-monitoring.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: az azure-Cloud Services Azure-erőforrásainak időforrásait a Microsoft fenntartja. Előfordulhat, hogy az ügyfeleknek olyan hálózati szabályt kell létrehozniuk, amely lehetővé teszi a környezetében használt időkiszolgálóhoz való hozzáférést az 123-as porton keresztül az UDP protokollal.

- [NTP-kiszolgáló elérése](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: az Azure Event Hubs használatával programozott módon használhatja a felhőalapú szolgáltatást. Integrálhatja és elküldheti ezeket az összes adattárat az Azure Sentinel szolgáltatásba a naplók monitorozásához és ellenőrzéséhez, vagy használjon harmadik féltől származó SIEM-et. A központi biztonsági naplók kezeléséhez konfigurálja a kiválasztott Azure Security Center-adatainak folyamatos exportálását az Azure Event Hubsba, és állítsa be a megfelelő összekötőt a SIEM-hez. Íme néhány lehetőség az Azure Sentinel szolgáltatáshoz, beleértve a külső gyártótól származó eszközöket:

- Azure Sentinel – a natív Security Center riasztások adatösszekötő használata
- Splunk – a splunk Azure Monitor-bővítményének használata
- IBM QRadar – manuálisan konfigurált naplózási forrás használata
- ArcSight – a SmartConnector használata

Tekintse át az Azure Sentinel dokumentációját az elérhető összekötők és az Azure Sentinel további részleteiért. 

- [Adatforrások csatlakoztatása](../sentinel/connect-data-sources.md)

- [Integrálás SIEM-mel](../security-center/continuous-export.md)

- [Diagnosztikai adattárolók tárolása](diagnostics-extension-to-storage.md)

- [SIEM-integráció konfigurálása az Azure Event Hubs használatával](../security-center/continuous-export.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: konfigurálja a Visual studiót az Azure Cloud Services hibaelhárításához Azure Diagnostics beállításához, amely rögzíti a rendszer-és naplózási adatait a virtuális gépeken, beleértve az azure-Cloud Services futtató virtuálisgép-példányokat is. A diagnosztikai adatait a rendszer az Ön által választott Storage-fiókba helyezi át. A diagnosztika bekapcsolása az Azure Cloud Services-projektekben az üzembe helyezés előtt.

 
Megtekintheti a tevékenység naplójában lévő egyes események változási előzményeit Azure Monitoron belül. Naplózza, hogy milyen változások történtek egy adott esemény időtartama alatt. Válasszon ki egy eseményt a tevékenység naplójában a Change History (előzetes verzió) lap mélyebb ellenőrzéséhez. A diagnosztikai adatok elküldése Application Insightsra, amikor Azure-Cloud Services tesz közzé a Visual studióból. Hozza létre a Application Insights Azure-erőforrást az adott időpontban, vagy küldje el az adott Azure-erőforrásnak. 

Az Azure Cloud Services a rendelkezésre állás, a teljesítmény, a hibák és a használat Application Insights figyelésére használható. Az egyéni diagramok hozzáadhatók a Application Insightshoz, így a leginkább fontos információk láthatók. A szerepkör-példány adatait az Azure Cloud Services projektben található Application Insights SDK-val lehet gyűjteni. 

- [A diagnosztika bekapcsolása a Visual Studióban az üzembe helyezés előtt](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Változási előzmények megtekintése](../azure-monitor/essentials/activity-log.md#view-change-history)

- [Azure Cloud Service Application Insights (klasszikus)](../azure-monitor/app/cloudservices.md)

- [Diagnosztika beállítása az Azure Cloud Service (klasszikus) és a Virtual Machines szolgáltatáshoz](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: az Azure Cloud Services speciális figyelését használhatja, amely lehetővé teszi a további mérőszámok mintavételezését és gyűjtését 5 perc, 1 óra és 12 óra intervallumban. Az összesített adatokat a rendszer a Storage-fiókban tárolja, és 10 nap múlva törlődik. A használt Storage-fiók azonban szerepkör szerint van konfigurálva, és különböző tárolási fiókokat használhat a különböző szerepkörökhöz. Ez a. csdef és a. cscfg fájlokban lévő kapcsolatok karakterláncával van konfigurálva.

Vegye figyelembe, hogy a speciális figyelés a figyelni kívánt szerepkörön a Azure Diagnostics bővítmény (Application Insights SDK opcionális) használatát foglalja magában. A diagnosztikai bővítmény egy Diagnostics. wadcfgx nevű konfigurációs fájlt használ a megfigyelt diagnosztikai metrikák konfigurálásához. Az Azure diagnosztikai bővítmény egy Azure Storage-fiókban gyűjti és tárolja az adatokat. Ezek a beállítások a. wadcfgx, a. csdef és a. cscfg fájlokban vannak konfigurálva.

- [Bevezetés a Cloud Service Monitorozásba](cloud-services-how-to-monitor.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: alapszintű vagy speciális figyelési üzemmódok érhetők el az Azure Cloud Services számára. Az Azure Cloud Services automatikusan gyűjti az alapszintű figyelési adatokat (CPU-százalékos, hálózati/kimenő és lemezes olvasási/írási) a gazdagép virtuális gépről. Tekintse meg a Azure Portal egy felhőalapú szolgáltatás áttekintés és mérőszámok lapjain gyűjtött figyelési adatokat. 

A diagnosztika engedélyezése az Azure Cloud Servicesban diagnosztikai adatok, például az alkalmazások naplói, a teljesítményszámlálók és sok más, az Azure Diagnostics bővítmény használata esetén. Engedélyezheti vagy frissítheti a diagnosztikai konfigurációt egy olyan felhőalapú szolgáltatáson, amely már fut Set-AzureServiceDiagnosticsExtension parancsmaggal, vagy üzembe helyez egy felhőalapú szolgáltatást a diagnosztikai bővítménnyel automatikusan. Szükség esetén telepítse az Application Insights SDK-t. Teljesítményszámlálók küldése Azure Monitorra.

Az Azure diagnosztikai bővítmény egy Azure Storage-fiókban gyűjti és tárolja az adatokat. Diagnosztikai adatok átvitele a Microsoft Azure Storage Emulatorba vagy az Azure Storage-ba, mivel az nem véglegesen tárolódik. A tárolás után a szolgáltatás számos elérhető eszközzel megtekinthető, például a Visual Studióban található Server Explorer, Microsoft Azure Storage Explorer, Azure Management Studio. Konfigurálja a diagnosztikai metrikákat, hogy a diagnosztika. wadcfgx nevű konfigurációs fájllal (szerepkör) legyenek figyelve a diagnosztikai bővítményben. 

- [Bevezetés a Cloud Service Monitorozásba](cloud-services-how-to-monitor.md)

- [Diagnosztika engedélyezése feldolgozói szerepkörben – integrálás egy SIEM-sel](../security-center/continuous-export.md)

- [Diagnosztika engedélyezése az Azure Cloud Services a PowerShell használatával](cloud-services-diagnostics-powershell.md)

- [Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban](./diagnostics-extension-to-storage.md?preserve-view=)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az azure-Cloud Services naplózási információinak figyelése az Azure Sentinel vagy egy harmadik féltől származó Siem-mel való integráció révén, a rendellenes tevékenységek riasztásának engedélyezésével.

- [Integrálás SIEM-mel](../security-center/continuous-export.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: az Azure-hoz készült Microsoft antimalware védi az azure-Cloud Servicesokat és a virtuális gépeket. Lehetősége van harmadik féltől származó biztonsági megoldások üzembe helyezésére is, például a webalkalmazási tűzoltó falakra, a hálózati tűzfalakra, az antimalware-ra, a behatolás észlelésére és megelőzésére szolgáló rendszerekre (AZONOSÍTÓk vagy IP-címek) és egyebekre.

- [Mik azok a funkciók és képességek, amelyeket az Azure alapszintű IP-címei/azonosítója és a DDOS biztosít](./cloud-services-configuration-and-management-faq.md#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Microsoft azt javasolja, hogy az Azure-erőforrásokhoz való hozzáférést az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével kezelje. Az Azure Cloud Services azonban nem támogatja az Azure RBAC modellt, mivel nem Azure Resource Manager-alapú szolgáltatás, és klasszikus előfizetést kell használnia

Alapértelmezés szerint a fiók rendszergazdája, a szolgáltatás rendszergazdája és a Co-Administrator az Azure-ban a három klasszikus előfizetés-rendszergazdai szerepkör. 

A hagyományos előfizetés-rendszergazdák teljes körű hozzáféréssel rendelkeznek az Azure-előfizetéshez. Az Azure Portal, Azure Resource Manager API-k és a klasszikus üzemi modell segítségével végzik az erőforrások felügyeletét. Az Azure-beli regisztrációhoz használt fiók lesz automatikusan a fiókadminisztrátor és a szolgáltatás-rendszergazda. Később további Co-Administrators is hozzáadhatók. 

A szolgáltatás-rendszergazda és a Co-Administrators egyenértékű hozzáféréssel rendelkezik azon felhasználókhoz, akik a tulajdonosi szerepkörhöz (egy Azure-szerepkörhöz) hozzá lettek rendelve az előfizetés hatókörében. Kezelje Co-Administrators vagy tekintse meg a szolgáltatás-rendszergazdát a Azure Portal klasszikus rendszergazdák lapján. 

Szerepkör-hozzárendelések listázása a klasszikus szolgáltatás-rendszergazda és a PowerShell-lel együtt a paranccsal:

Get-AzRoleAssignment – IncludeClassicAdministrators

Tekintse át a klasszikus előfizetés felügyeleti szerepkörei közötti különbségeket. 

- [Három klasszikus előfizetés felügyeleti szerepkör közötti különbségek](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: javasoljuk, hogy hozzon létre szabványos működési eljárásokat a dedikált rendszergazdai fiókok használata során, a rendelkezésre álló szerepkörök alapján, valamint az Azure-Cloud Services erőforrásainak üzemeltetéséhez és kezeléséhez szükséges engedélyekkel.

- [A klasszikus előfizetés felügyeleti szerepkörei közötti különbségek](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az Azure Cloud Services-on futó alkalmazások külön identitások kezelésének elkerülése. Az egyszeri bejelentkezés megvalósításával elkerülhető, hogy a felhasználók több identitást és hitelesítő adatot kezeljenek.

- [Mi az egyszeri bejelentkezés (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: ajánlott egy biztonságos, Azure által felügyelt munkaállomás (más néven privilegizált hozzáférési munkaállomás) használata a felügyeleti feladatokhoz, amelyhez emelt szintű jogosultságok szükségesek.

- [A biztonságos, Azure által felügyelt munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [A Azure Active Directory (Azure AD) többtényezős hitelesítésének engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: az Azure Cloud Service REST API-k segítségével leltárba hozhatja az Azure Cloud Service-erőforrásokat bizalmas adatokhoz. Az üzembe helyezett Cloud Service-erőforrások lekérdezése a konfigurációs és a. pkg-erőforrások beszerzéséhez.

 Például néhány API az alábbi listában látható:

- Telepítés beolvasása – a központi telepítés beolvasása művelet a központi telepítés konfigurációs információit, állapotát és rendszertulajdonságait adja vissza.
- Csomag beolvasása – a csomag beolvasása művelet lekérdezi egy felhőalapú szervizcsomagot a központi telepítéshez, és a Microsoft Azure Blob Storage tárolóban tárolja a csomagokat.
- Cloud Service-tulajdonságok beolvasása – a Cloud Service tulajdonságok beolvasása művelet lekéri a megadott felhőalapú szolgáltatás tulajdonságait

Tekintse át az Azure Cloud Service REST API-jai dokumentációját, és hozzon létre egy folyamatot a bizalmas adatok védelméhez a szervezeti követelmények alapján.

- [Központi telepítő beolvasása](/rest/api/compute/cloudservices/rest-get-deployment)

- [Cloud Service-tulajdonságok beolvasása](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Csomag beolvasása](/rest/api/compute/cloudservices/rest-get-package)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezeti típushoz és az adatérzékeny szinthez az Azure Cloud Services.

Az Azure Cloud Service Certificate elem "permissionLevel" tulajdonságát is szerkesztheti, hogy megadja a szerepkör-folyamatokhoz megadott hozzáférési engedélyeket. Ha azt szeretné, hogy csak emelt szintű folyamatok férhessenek hozzá a titkos kulcshoz, adja meg a emelt szintű engedélyt. a limitedOrElevated engedély lehetővé teszi az összes szerepkör-folyamat számára a titkos kulcs elérését. A lehetséges értékek limitedOrElevated vagy emelt szintűek. Az alapértelmezett érték a limitedOrElevated.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [WebRole-séma](./schema-csdef-webrole.md#Certificate)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: javasoljuk, hogy használjon külső gyártótól származó megoldást az Azure Marketplace-ről a hálózati környezetekben a bizalmas adatok jogosulatlan átvitelének figyelésére és az ilyen átvitelek blokkolására, miközben az információs biztonsági szakemberek riasztást küldenek.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a TLS v2 konfigurálása az Azure Cloud Serviceshoz. A Azure Portal használatával adja hozzá a tanúsítványt a szakaszos Azure Cloud Services üzembe helyezéséhez, és adja hozzá a tanúsítvány információit a szolgáltatások CSDEF és CSCFG fájljaihoz. Csomagolja újra az alkalmazást, és frissítse a szakaszos üzembe helyezést az új csomag használatára. 

Az Azure-Cloud Serviceshoz csatolt szolgáltatás-tanúsítványok használata, amelyek lehetővé teszik a szolgáltatással és a szolgáltatással való biztonságos kommunikációt. Olyan tanúsítványt adjon meg, amely képes hitelesíteni a kitett HTTPS-végpontot. Adja meg a szolgáltatási tanúsítványokat a Cloud Service-definícióban, és automatikusan telepítse azokat a virtuális gépre, amely futtatja a szerepkör egy példányát.

Hitelesítés a felügyeleti API-val felügyeleti tanúsítványokkal – a felügyeleti tanúsítványok lehetővé teszik a hitelesítését a klasszikus üzemi modellel. Számos program és eszköz (például a Visual Studio vagy az Azure SDK) ezeket a tanúsítványokat használja a különböző Azure-szolgáltatások konfigurációjának és üzembe helyezésének automatizálására. 

A klasszikus üzembe helyezési modell API-val kapcsolatos további információkért a Azure Portalon keresztül elérhető klasszikus üzemi modell-funkciók programozott hozzáférését biztosítjuk. A Pythonhoz készült Azure SDK használható az Azure-Cloud Services és az Azure Storage-fiókok kezelésére. A Pythonhoz készült Azure SDK becsomagolja a klasszikus üzembe helyezési modell API-t, amely egy REST API. Minden API-művelet a TLS protokollon keresztül történik, és az X. 509 v3 tanúsítványok használatával kölcsönösen hitelesítve van. A kezelési szolgáltatás az Azure-ban futó szolgáltatáson belülről érhető el. Emellett közvetlenül az interneten keresztül is elérhető bármely olyan alkalmazásból, amely képes HTTPS-kéréseket küldeni és HTTPS-válaszokat fogadni.

- [TLS konfigurálása alkalmazáshoz az Azure-ban](cloud-services-configure-ssl-certificate-portal.md)

- [A klasszikus üzembe helyezési modell használata a Pythonból](cloud-services-python-how-to-use-service-management.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: ajánlott egy külső féltől származó aktív felderítési eszköz használata a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított összes bizalmas információ azonosítására, beleértve a helyszínen vagy egy távoli szolgáltatón keresztül, majd a szervezet bizalmas információinak leltározását.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható a Cloud Service-re (klasszikus). Nem kényszeríti az adatvesztés megelőzését.

Javasoljuk, hogy hozzon létre egy külső gyártótól származó eszközt, például egy automatizált gazdagép-alapú adatvesztés-megelőzési megoldást, hogy az adathozzáférés-vezérlést még akkor is kényszerítse, ha az Adatmásolás egy rendszeren történik.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az Azure Cloud Services nem támogatja a titkosítást. Ennek az az oka, hogy az Azure Cloud Services úgy lett kialakítva, hogy állapot nélküli legyen. Az Azure Cloud Services támogatja a külső tárterületet (például az Azure Storage-t), amely alapértelmezés szerint titkosított a nyugalmi állapotban.  

Az ideiglenes lemezekben tárolt alkalmazásadatok nem titkosítottak. Az ügyfél feladata, hogy szükség szerint felügyelje és titkosítsa ezeket az adatvédelmet.  

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor klasszikus metrikai riasztásokat használhat, ha értesítést szeretne kapni, ha a kritikus erőforrásokra alkalmazott mérőszámok egyike küszöbértéket tesz elérhetővé. A klasszikus metrikai riasztások olyan régebbi funkciók, amelyek csak a nem dimenziós metrikák esetében engedélyezik a riasztást. Létezik egy metrikus riasztások nevű meglévő újabb funkció, amely továbbfejlesztett funkciókat biztosít a klasszikus metrikai riasztások során. 

Emellett a Application Insights képes figyelni az Azure Cloud Services-alkalmazásokat a rendelkezésre állás, a teljesítmény, a hibák és a használat tekintetében. Ez a Application Insights SDK-k összesített adatait használja az Azure-Cloud Services Azure Diagnostics adataival.

- [Klasszikus metrikai riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../azure-monitor/alerts/alerts-classic-portal.md)

- [Metrikus riasztások áttekintése](../azure-monitor/alerts/alerts-metric-overview.md) 

- [Azure Cloud Service Application Insights (klasszikus)](../azure-monitor/app/cloudservices.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: ez az információ az Azure vendég operációs rendszerre vonatkozik az Azure Cloud Services Worker és a webes szerepkörökhöz a szolgáltatásként nyújtott platformmal (Pásti). Ez azonban nem vonatkozik a szolgáltatásként szolgáló infrastruktúra (IaaS) Virtual Machinesra.

Alapértelmezés szerint az Azure rendszeresen frissíti az ügyfél vendég operációs rendszerét az operációs rendszer azon családján belüli legújabb támogatott lemezképre, amelyet a szolgáltatási konfigurációban (. cscfg) adott meg, például Windows Server 2016.

Amikor egy ügyfél egy adott operációsrendszer-verziót választ az Azure Cloud Services üzemelő példányához, letiltja az operációs rendszer automatikus frissítését, és javítja a felelősségét. Az ügyfélnek biztosítania kell, hogy szerepkör-példányai frissítéseket fogadnak, vagy az alkalmazásuk biztonsági rések számára elérhetővé tehetik.

- [Azure vendég operációs rendszer](cloud-services-guestos-msrc-releases.md)

- [Az Azure vendég operációs rendszer támogatása és a nyugdíjazási szabályzat](cloud-services-guestos-retirement-policy.md)

- [A Cloud Service konfigurálása (klasszikus)](cloud-services-how-to-configure-portal.md)

- [Vendég operációs rendszer verziójának kezelése](./cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: automatikus javítási megoldás üzembe helyezése harmadik féltől származó szoftverek címeihez

**Útmutató**: külső gyártótól származó javítási felügyeleti megoldás használata. Azok az ügyfelek, akik már használnak Configuration Manager a környezetben, System Center Updates Publisher is használhatják, így egyéni frissítéseket tehetnek közzé a Windows Server Update szolgáltatásban. 

Ez lehetővé teszi, hogy Update Management a harmadik féltől származó szoftverrel rendelkező frissítési adattárként Configuration Manager használó gépeket.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: azt javasoljuk, hogy az ügyfelek folyamatosan megértsék a kockázatuk körét a DDoS-támadásoktól. 

Javasoljuk a következő forgatókönyvek megvalósítását:

- Milyen új, nyilvánosan elérhető Azure-erőforrásokra van szükségük a védelemhez?
- Van egy meghibásodási pont a szolgáltatásban?
- Hogyan különíthetők el a szolgáltatások a támadás hatásának korlátozásával, miközben a szolgáltatások az érvényes ügyfelek számára is elérhetővé válnak?
- Vannak olyan virtuális hálózatok, amelyeken engedélyezve van a DDoS Protection standard, de nem?
- A szolgáltatásom aktív/aktív a több régióban feladatátvételsel?

Támogató dokumentáció:

- [Az Azure-erőforrások kockázati kiértékelése](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: nem alkalmazható az Azure Cloud Servicesra. Ez a javaslat a számítási erőforrások IaaS vonatkozik.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: javasoljuk, hogy rendszeresen összehangolja a leltárt, és gondoskodjon arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: az ügyfélnek jóváhagyott Azure-erőforrásokat és jóváhagyott szoftvereket kell meghatároznia a számítási erőforrásokhoz.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Security Centerban elérhető adaptív alkalmazás-vezérlési funkció használata. Ez egy intelligens, automatizált, teljes körű megoldás a Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak Windows-és Linux-, Azure-és nem Azure-alapú gépeken. Emellett segít megerősíteni a gépeket a kártevők ellen. 

Ez a szolgáltatás az Azure-ban és a nem Azure-beli Windowsban (minden verzió, klasszikus vagy Azure Resource Manager) és linuxos gépen is elérhető.

Security Center a gépi tanulás segítségével elemzi a gépen futó alkalmazásokat, és létrehoz egy engedélyezési listát ebből az intelligenciával. Ez a funkció nagy mértékben leegyszerűsíti az alkalmazások engedélyezési listájának szabályzatának konfigurálását és karbantartását, így lehetővé teszi a következőket:
- A kártékony alkalmazások futtatására tett kísérletek blokkolása vagy riasztása, beleértve azokat is, amelyeket antimalware-megoldások okozhatnak.

- A cég vagy szervezet biztonsági házirendjének való megfelelést, amely csak a licencelt szoftverek használatát engedélyezi.
- A nem kívánt szoftverek adott környezetben való használatának megelőzését.
- Az elavult és nem támogatott alkalmazások futtatásának letiltását.
- A szervezetben nem engedélyezett szoftvereszközök használatának megelőzését.
- Azt, hogy az informatikai részleg szabályozhassa a bizalmas adatokhoz való hozzáférést az alkalmazások használata során.

További részletek a hivatkozott hivatkozásokon érhetők el.

- [Adaptív alkalmazásvezérlők](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: a Azure Security Centerban elérhető adaptív alkalmazás-vezérlési funkció használata. Ez egy intelligens, automatizált, teljes körű megoldás a Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak Windows-és Linux-, Azure-és nem Azure-alapú gépeken. Emellett segít megerősíteni a gépeket a kártevők ellen. 

Ez a szolgáltatás az Azure-ban és a nem Azure-beli Windowsban (minden verzió, klasszikus vagy Azure Resource Manager) és linuxos gépen is elérhető.

Security Center a gépi tanulás segítségével elemzi a gépen futó alkalmazásokat, és létrehoz egy engedélyezési listát ebből az intelligenciával. Ez a funkció nagy mértékben leegyszerűsíti az alkalmazások engedélyezési listájának szabályzatának konfigurálását és karbantartását, így lehetővé teszi a következőket:

- A kártékony alkalmazások futtatására tett kísérletek blokkolása vagy riasztása, beleértve azokat is, amelyeket antimalware-megoldások okozhatnak.

- A cég vagy szervezet biztonsági házirendjének való megfelelést, amely csak a licencelt szoftverek használatát engedélyezi.

- A nem kívánt szoftverek adott környezetben való használatának megelőzését.

- Az elavult és nem támogatott alkalmazások futtatásának letiltását.

- A szervezetben nem engedélyezett szoftvereszközök használatának megelőzését.

- Azt, hogy az informatikai részleg szabályozhassa a bizalmas adatokhoz való hozzáférést az alkalmazások használata során.

További részletek a hivatkozott hivatkozásokon érhetők el.

- [Adaptív alkalmazásvezérlők](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: a Azure Security Centerban elérhető adaptív alkalmazás-vezérlési funkció használata. Ez egy intelligens, automatizált, teljes körű megoldás a Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak Windows-és Linux-, Azure-és nem Azure-alapú gépeken. Emellett segít megerősíteni a gépeket a kártevők ellen. 

Ez a szolgáltatás az Azure-ban és a nem Azure-beli Windowsban (minden verzió, klasszikus vagy Azure Resource Manager) és linuxos gépen is elérhető.

Security Center a gépi tanulás segítségével elemzi a gépen futó alkalmazásokat, és létrehoz egy engedélyezési listát ebből az intelligenciával. Ez a funkció nagy mértékben leegyszerűsíti az alkalmazások engedélyezési listájának szabályzatának konfigurálását és karbantartását, így lehetővé teszi a következőket:

- A kártékony alkalmazások futtatására tett kísérletek blokkolása vagy riasztása, beleértve azokat is, amelyeket antimalware-megoldások okozhatnak.

- A cég vagy szervezet biztonsági házirendjének való megfelelést, amely csak a licencelt szoftverek használatát engedélyezi.

- A nem kívánt szoftverek adott környezetben való használatának megelőzését.

- Az elavult és nem támogatott alkalmazások futtatásának letiltását.

- A szervezetben nem engedélyezett szoftvereszközök használatának megelőzését.

- Azt, hogy az informatikai részleg szabályozhassa a bizalmas adatokhoz való hozzáférést az alkalmazások használata során.

További részletek a hivatkozott hivatkozásokon érhetők el.

- [Adaptív alkalmazásvezérlők](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: a Azure Security Centerban elérhető adaptív alkalmazás-vezérlési funkció használata. Ez egy intelligens, automatizált, teljes körű megoldás a Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak Windows-és Linux-, Azure-és nem Azure-alapú gépeken. Emellett segít megerősíteni a gépeket a kártevők ellen. 

Ez a szolgáltatás az Azure-ban és a nem Azure-beli Windowsban (minden verzió, klasszikus vagy Azure Resource Manager) és linuxos gépen is elérhető.

Security Center a gépi tanulás segítségével elemzi a gépen futó alkalmazásokat, és létrehoz egy engedélyezési listát ebből az intelligenciával. Ez a funkció nagy mértékben leegyszerűsíti az alkalmazások engedélyezési listájának szabályzatának konfigurálását és karbantartását, így lehetővé teszi a következőket:

- A kártékony alkalmazások futtatására tett kísérletek blokkolása vagy riasztása, beleértve azokat is, amelyeket antimalware-megoldások okozhatnak.

- A cég vagy szervezet biztonsági házirendjének való megfelelést, amely csak a licencelt szoftverek használatát engedélyezi.

- A nem kívánt szoftverek adott környezetben való használatának megelőzését.

- Az elavult és nem támogatott alkalmazások futtatásának letiltását.

- A szervezetben nem engedélyezett szoftvereszközök használatának megelőzését.

- Azt, hogy az informatikai részleg szabályozhassa a bizalmas adatokhoz való hozzáférést az alkalmazások használata során.

További részletek a hivatkozott hivatkozásokon érhetők el.

- [Adaptív alkalmazásvezérlők](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: a Azure Security Centerban elérhető adaptív alkalmazás-vezérlési funkció használata. Ez egy intelligens, automatizált, teljes körű megoldás a Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak Windows-és Linux-, Azure-és nem Azure-alapú gépeken. Emellett segít megerősíteni a gépeket a kártevők ellen. 

Ez a szolgáltatás az Azure-ban és a nem Azure-beli Windowsban (minden verzió, klasszikus vagy Azure Resource Manager) és linuxos gépen is elérhető.

Security Center a gépi tanulás segítségével elemzi a gépen futó alkalmazásokat, és létrehoz egy engedélyezési listát ebből az intelligenciával. Ez a funkció nagy mértékben leegyszerűsíti az alkalmazások engedélyezési listájának szabályzatának konfigurálását és karbantartását, így lehetővé teszi a következőket:
- A kártékony alkalmazások futtatására tett kísérletek blokkolása vagy riasztása, beleértve azokat is, amelyeket antimalware-megoldások okozhatnak.

- A cég vagy szervezet biztonsági házirendjének való megfelelést, amely csak a licencelt szoftverek használatát engedélyezi.
- A nem kívánt szoftverek adott környezetben való használatának megelőzését.
- Az elavult és nem támogatott alkalmazások futtatásának letiltását.
- A szervezetben nem engedélyezett szoftvereszközök használatának megelőzését.
- Azt, hogy az informatikai részleg szabályozhassa a bizalmas adatokhoz való hozzáférést az alkalmazások használata során.

További részletek a hivatkozott hivatkozásokon érhetők el.

- [Adaptív alkalmazásvezérlők](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokban

**Útmutató**: a Azure Security Centerban elérhető adaptív alkalmazás-vezérlési funkció használata. Ez egy intelligens, automatizált, teljes körű megoldás a Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak Windows-és Linux-, Azure-és nem Azure-alapú gépeken. Emellett segít megerősíteni a gépeket a kártevők ellen. 

Ez a szolgáltatás az Azure-ban és a nem Azure-beli Windowsban (minden verzió, klasszikus vagy Azure Resource Manager) és linuxos gépen is elérhető.

Security Center a gépi tanulás segítségével elemzi a gépen futó alkalmazásokat, és létrehoz egy engedélyezési listát ebből az intelligenciával. Ez a funkció nagy mértékben leegyszerűsíti az alkalmazások engedélyezési listájának szabályzatának konfigurálását és karbantartását, így lehetővé teszi a következőket:

- A kártékony alkalmazások futtatására tett kísérletek blokkolása vagy riasztása, beleértve azokat is, amelyeket antimalware-megoldások okozhatnak.

- A cég vagy szervezet biztonsági házirendjének való megfelelést, amely csak a licencelt szoftverek használatát engedélyezi.

- A nem kívánt szoftverek adott környezetben való használatának megelőzését.

- Az elavult és nem támogatott alkalmazások futtatásának letiltását.

- A szervezetben nem engedélyezett szoftvereszközök használatának megelőzését.

- Azt, hogy az informatikai részleg szabályozhassa a bizalmas adatokhoz való hozzáférést az alkalmazások használata során.

További részletek a hivatkozott hivatkozásokon érhetők el.

- [Adaptív alkalmazásvezérlők](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az Azure Cloud Services érzékeny vagy magas kockázatú alkalmazásaihoz külön előfizetéseket vagy felügyeleti csoportokat kell létrehozni az elkülönítés biztosításához.

Használjon hálózati biztonsági csoportot, hozzon létre egy bejövő biztonsági szabályt, válasszon ki egy szolgáltatást, például a http-t, válasszon egy egyéni portot is, és adjon meg egy prioritást és egy nevet. A prioritás befolyásolja azt a sorrendet, amelyben a szabályok alkalmazása történik, annál kisebb a numerikus érték, annál korábbi a szabály. A hálózati biztonsági csoportot egy alhálózathoz vagy egy adott hálózati adapterhez kell rendelni a hálózati forgalom elkülönítéséhez vagy szegmentálásához az üzleti igények alapján.

További részletek a hivatkozott hivatkozásokon érhetők el.

- [Oktatóanyag – hálózati adatforgalom szűrése hálózati biztonsági csoporttal a Azure Portal használatával](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Security Center ajánlásainak használata az Azure-Cloud Services erőforrásainak biztonságos alapkonfigurációjának megfelelően. 

A Azure Portal válassza a Security Center, majd a számítási &amp; alkalmazások és az Azure Cloud Services lehetőséget a szolgáltatási erőforrásokra vonatkozó ajánlások megtekintéséhez.

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: nem alkalmazható az Azure Cloud Servicesra. Ez a klasszikus üzembehelyezési modellen alapul. Ajánlott külső gyártótól származó megoldást használni a biztonságos Azure-erőforrás-konfigurációk fenntartásához

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure Cloud Service konfigurációs fájlja az erőforrások működési attribútumait tárolja. A konfigurációs fájlok másolatát biztonságos Storage-fiókba is tárolhatja.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: nem alkalmazható az Azure Cloud Servicesra. Ez a klasszikus üzemi modellen alapul, és nem felügyelhető Azure Resource Manager üzembe helyezés-alapú konfigurációs eszközeivel.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható az Azure Cloud Servicesra. Ez az ajánlás az infrastruktúra-szolgáltatás (IaaS) alapú számítási erőforrások esetében alkalmazható.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center használata az Azure-erőforrások alapkonfigurációjának vizsgálatához.  

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: Azure Security Centerban válassza &amp; a számítási alkalmazások lehetőséget, és kövesse a virtuális gépek, kiszolgálók és tárolók javaslatait.

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../security-center/container-security.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure Cloud Services egy klasszikus üzemi modellen alapul, és nem integrálható a Azure Key Vaultokkal.

A titkos kulcsokat, például az Azure Cloud Services használt hitelesítő adatokat is biztonságossá teheti, így nem kell minden alkalommal jelszót beírnia. A kezdéshez egy egyszerű szöveges jelszót kell megadnia, amely a ConvertTo-SecureString, a PowerShell-parancs segítségével konvertálja biztonságos karakterlánccá. Ezután alakítsa át ezt a biztonságos karakterláncot egy titkosított szabványos karakterlánccá a ConvertFrom-SecureString használatával.  Ezt a titkosított szabványos karakterláncot mentheti egy fájlra a set-Content paranccsal.

Emellett ajánlott a titkos kulcsok tárolása az Azure Cloud Services-ban használt tanúsítványokhoz egy biztonságos tárolóban.

- [Távoli asztal konfigurálása a PowerShellből](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: az Azure Cloud Servicesban használt hitelesítő adatok védelme, így nem kell minden alkalommal jelszót beírnia. 
 

A kezdéshez egy egyszerű szöveges jelszót kell megadnia, amely a ConvertTo-SecureString, a PowerShell-parancs használatával módosítható egy biztonságos karakterláncra. Ezután alakítsa át ezt a biztonságos karakterláncot egy titkosított szabványos karakterlánccá a ConvertFrom-SecureString használatával. Most mentse ezt a titkosított szabványos karakterláncot egy fájlba Set-Content parancs használatával.

Tárolja a titkos kulcsokat az Azure Cloud Servicesban használt tanúsítványokhoz egy biztonságos tárolási helyre.

- [Távoli asztal konfigurálása a PowerShellből](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: központilag felügyelt kártevő szoftver használata

**Útmutató**: az Azure-hoz készült Microsoft antimalware az Azure Cloud Services és Virtual Machines számára érhető el. Ez egy ingyenes, valós idejű védelem, amely segít a vírusok, kémprogramok és más kártevő szoftverek azonosításában és eltávolításában. Riasztásokat állít elő, amikor az ismert kártékony vagy nemkívánatos szoftverek megkísérlik telepíteni vagy futtatni magukat az Azure-rendszereken. 

Használja a PowerShell-alapú antimalware-parancsmagot a "Get-AzureServiceAntimalwareConfig" nevű antimalware-konfiguráció beszerzéséhez.

Engedélyezze a antimalware bővítményt egy PowerShell-parancsfájllal az Azure Cloud Services indítási feladatában.

Válassza ki az adaptív alkalmazás-vezérlés funkciót Azure Security Center, egy intelligens, automatizált, teljes körű megoldás. Segít megerősíteni a gépeket a kártevők ellen, és lehetővé teszi a kártékony alkalmazások futtatására tett kísérletek letiltását vagy riasztását, beleértve azokat is, amelyeket antimalware-megoldások okozhatnak.

- [Hogyan adhatok hozzá antimalware-bővítményt az Azure-Cloud Services automatizált módon](./cloud-services-configuration-and-management-faq.md#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Kártevő szoftverek üzembe helyezésének forgatókönyvei](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [Adaptív alkalmazásvezérlők](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal. 

Egyértelműen megjelölheti az előfizetéseket (például éles környezetben, nem éles környezetben), és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet. 

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után. 

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. 

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md) 

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

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
