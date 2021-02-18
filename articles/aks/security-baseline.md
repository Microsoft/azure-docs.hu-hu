---
title: Azure biztonsági alapkonfiguráció az Azure Kubernetes Service-hez
description: Az Azure Kubernetes szolgáltatás biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8e86679e7531bb70d6d4f1a27718c2fc8f0e8a9c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578537"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Azure biztonsági alapkonfiguráció az Azure Kubernetes Service-hez

Az Azure Kubernetes szolgáltatás Azure-beli biztonsági alapterve olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](../security/benchmarks/security-control-network-security.md)című témakörben talál.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: alapértelmezés szerint a rendszer automatikusan létrehoz egy hálózati biztonsági csoportot és egy útválasztási táblázatot egy Microsoft Azure Kubernetes Service (ak) fürt létrehozásával. Az AK automatikusan módosítja a hálózati biztonsági csoportokat a megfelelő adatforgalomhoz, mivel a szolgáltatások terheléselosztással, Port-hozzárendeléssel vagy bejövő útvonalakkal jönnek létre. A hálózati biztonsági csoport automatikusan társítva lesz a virtuális hálózati adapterekhez az ügyfél-csomópontokon és az útválasztási táblában a virtuális hálózat alhálózatával. 

A hálózati forgalom korlátozásához használja az AK hálózati házirendeket, hogy meghatározza a bejövő és a kimenő adatforgalomra vonatkozó szabályokat a fürt Linux-hüvelyei között, a névterek és a jelölők kiválasztása alapján. A hálózati házirend használatához az Azure CNI beépülő modulnak definiált virtuális hálózattal és alhálózatokkal kell rendelkeznie, és csak a fürt létrehozásakor engedélyezhető. Nem helyezhetők üzembe meglévő AK-fürtön.

Létrehozhat egy privát AK-fürtöt, amely biztosítja, hogy az AK API-kiszolgálója és a csomópont-készletek közötti hálózati forgalom csak a magánhálózaton maradjon. A vezérlő síkja vagy az API-kiszolgáló egy AK által felügyelt Azure-előfizetésben található, és belső (RFC1918) IP-címeket használ, míg az ügyfél fürtje vagy csomópont-készlete a saját előfizetésében van. A kiszolgáló és a fürt vagy a csomópont-készlet kommunikál egymással az API-kiszolgáló virtuális hálózatában lévő Azure Private link szolgáltatással, valamint egy privát végponttal, amely az ügyfél AK-fürtjének alhálózatán van kitéve.  Azt is megteheti, hogy nyilvános végpontot használ az AK API-kiszolgálóhoz, de korlátozza a hozzáférést az AK API-kiszolgáló jogosult IP-címtartományok szolgáltatásával. 

- [Az Azure Kubernetes Service (AKS) alkalmazásainak és fürtjeinek biztonsági fogalmai](concepts-security.md)

- [Biztonságos forgalom a hüvelyek között hálózati házirendek használatával az Azure Kubernetes szolgáltatásban (ak)](use-network-policies.md)

- [Privát Azure Kubernetes Service-fürt létrehozása](private-clusters.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) által használt hálózati erőforrások biztonságossá tételéhez használja a Security centert, és kövesse a hálózati védelmi javaslatait. 

Engedélyezze a hálózati biztonsági csoport adatfolyam-naplóit, és küldje el a naplókat egy Azure Storage-fiókba a naplózáshoz. A flow-naplókat Log Analytics munkaterületre is elküldheti, majd a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalmi mintákba a hálózati tevékenységek megjelenítéséhez, a gyors és biztonsági fenyegetések azonosításához, a forgalmi folyamatok mintáinak megismeréséhez, valamint a hálózati helytelen konfigurációhoz.

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

- [Hálózati biztonsági folyamatok naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: Azure Application Gateway enabled webalkalmazási TŰZFAL (WAF) használata egy AK-fürt előtt, hogy további biztonsági réteget biztosítson a webes alkalmazások bejövő forgalmának szűrésével. Az Azure WAF a nyílt webes alkalmazás biztonsági projektje (OWASP) által biztosított szabályok egy halmazát használja támadásokra, például a webhelyek parancsfájlkezelésére vagy a cookie-mérgezésre ezen a forgalomon keresztül. 

API-átjárót használhat a hitelesítéshez, az engedélyezéshez, a szabályozáshoz, a gyorsítótárazáshoz, az átalakításhoz és a figyeléshez az AK-környezetben használt API-khoz. Az API-átjárók a szolgáltatásokhoz való bevezető ajtóként szolgálnak, leválasztják az ügyfeleket a-szolgáltatásokból, és csökkentik a szolgáltatások összetettségét azáltal, hogy megszüntetik a több területet érintő problémáinak kezelési terheit.

- [A hálózati kapcsolat és a biztonság ajánlott eljárásainak ismertetése az AK-ban](operator-best-practices-network.md)

- [Bejövő Application Gateway vezérlő ](../application-gateway/ingress-controller-overview.md)

- [Az Azure API Management használata az Azure Kubernetes Service-ben üzembe helyezett Service-szolgáltatásokkal](../api-management/api-management-kubernetes.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: engedélyezze a Microsoft Distributed szolgáltatásmegtagadás (DDoS) standard szintű védelmét azon virtuális hálózatokon, amelyeken az Azure Kubernetes Service (ak) összetevői a DDOS-támadások elleni védelemre vannak telepítve.
Telepítse a hálózati házirend-motort, és hozzon létre Kubernetes hálózati házirendeket a hüvelyek közötti adatforgalom vezérléséhez az AK-ban, mivel alapértelmezés szerint a hüvelyek között minden forgalom engedélyezett. A hálózati házirendet csak a Linux-alapú csomópontok és a hüvelyek esetében kell használni az AK-ban. A pod-kommunikációt korlátozó szabályok meghatározása a fokozott biztonság érdekében. 

Megadhatja, hogy engedélyezi vagy megtagadja a forgalmat olyan beállítások alapján, mint a hozzárendelt címkék, a névtér vagy a forgalmi port. A szükséges hálózati házirendek automatikusan alkalmazhatók, mert a hüvelyek dinamikusan jönnek létre egy AK-fürtben. 

- [Biztonságos forgalom a hüvelyek között hálózati házirendek használatával az Azure Kubernetes szolgáltatásban (ak)](use-network-policies.md)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: a rendellenes tevékenységek kivizsgálásához szükség szerint használja Network Watcher-csomagok rögzítését. 

A Network Watcher automatikusan engedélyezve van a virtuális hálózat régiójában, amikor létrehoz vagy frissít egy virtuális hálózatot az előfizetésében. Network Watcher új példányait is létrehozhatja a PowerShell, az Azure CLI, a REST API vagy a ARMClient metódus használatával.

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) fürtjének biztonságossá tétele egy webalkalmazási TŰZFALLAL (WAF) rendelkező azure-Application Gateway. 

Ha a behatolás észlelése és/vagy megelőzése a hasznos adatok vizsgálata vagy a viselkedési elemzés alapján nem követelmény, az Azure-Application Gateway és a WAF használható "észlelési módban" a riasztások és fenyegetések naplózására, vagy "megelőzési mód", hogy aktívan blokkolja az észlelt behatolásokat és támadásokat.

- [Az AK-fürt WAF való biztonságossá tételéhez ajánlott eljárások ismertetése](operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [Az Azure Application Gateway (Azure WAF) üzembe helyezése](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: Azure Application Gateway-kompatibilis webalkalmazási TŰZFAL (WAF) használata egy AK-fürt előtt a bejövő forgalom szűréséhez. Az Open Web Application Security Project (OWASP) olyan szabályokat biztosít, amelyeket az Azure WAF használ az olyan támadások figyelésére, mint a helyközi parancsfájlok vagy a cookie-mérgezés.

Teljes tartománynevet (FQDN) tartalmazó címkéket alkalmazhat az alkalmazásokhoz, hogy könnyen használhatók legyenek egy hálózati biztonsági csoporton belüli alkalmazás-szabályok beállításához. A hálózati szabályok beállítása után. Adjon hozzá egy, a 443-es és a 80-as TCP-porton keresztül elérhető teljes TARTOMÁNYNEVEt tartalmazó szabályt a teljes TARTOMÁNYNEVEt használó alkalmazási szabályhoz, például AzureKubernetesService. 

- [A hálózati kapcsolat és a biztonság ajánlott eljárásainak ismertetése az AK-ban](operator-best-practices-network.md)

- [Biztonságos forgalom a hüvelyek között hálózati házirendek használatával az Azure Kubernetes szolgáltatásban (ak)](use-network-policies.md)

- [Az Azure Application Gateway (Azure WAF) üzembe helyezése](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a virtuális hálózati szolgáltatás címkéi használatával meghatározhatja az Azure Kubernetes szolgáltatás (ak) példányaihoz társított hálózati biztonsági csoportok hálózati hozzáférés-vezérléseit. A szolgáltatási címkék adott IP-címek helyett használhatók olyan biztonsági szabályok létrehozásakor, amelyek engedélyezik vagy megtagadják a kapcsolódó szolgáltatás forgalmát a szolgáltatási címke nevének megadásával. 

A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Alkalmazzon Azure-címkét a Node-készletekbe az AK-fürtben. Ezek nem azonosak a virtuális hálózati szolgáltatás címkével, és a rendszer a csomópont-készlet minden csomópontján alkalmazza őket, és a frissítéseken keresztül marad. 

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

- [Az AK-NSG ismertetése](support-policies.md)

- [A fürtcsomópontok kimenő forgalmának szabályozása az Azure Kubernetes szolgáltatásban (ak)](limit-egress-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása az Azure Kubernetes szolgáltatással (ak) rendelkező fürtökhöz társított hálózati erőforrásokkal Azure Policy. Használjon Azure Policy aliasokat a "Microsoft. Tárolószolgáltatás" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre az AK-fürtök hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. 

Emellett használjon az AK-hoz kapcsolódó beépített szabályzat-definíciókat is, például:

• A Kubernetes-szolgáltatásokban meg kell határozni a jóváhagyott IP-tartományokat

• HTTPS bejövő forgalom kikényszerítve a Kubernetes-fürtben

• Győződjön meg arról, hogy a szolgáltatások csak a Kubernetes-fürt engedélyezett portjain figyelik

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a hálózati biztonsági csoportokhoz és más erőforrásokhoz használt címkék használata az Azure Kubernetes Service-(ak-) fürtökre irányuló és onnan érkező adatforgalomhoz. A "Description" (Leírás) mező használatával az egyes hálózati biztonsági csoportokra vonatkozó szabályok megadásával meghatározhatja az üzleti igényeket és/vagy időtartamot, és így tovább, minden olyan szabály esetében, amely engedélyezi a hálózati forgalmat.
Használja a beépített Azure Policy címkézéssel kapcsolatos definíciókat, például a "címke és az érték megkövetelése" kifejezést, amely biztosítja, hogy az összes erőforrás címkével legyen létrehozva, és értesítést kapjon a meglévő címkézetlen erőforrásokról.

Engedélyezheti vagy megtagadhatja a fürtön belüli adott hálózati elérési utakat a névterek és a hálózati házirendekkel rendelkező címkék kiválasztása alapján. Ezeket a névtereket és címkéket leíróként használhatja a forgalmi konfigurációs szabályokhoz. A Azure PowerShell vagy az Azure parancssori felület (CLI) használatával kereshet vagy végezhet műveleteket az erőforrásokon a címkék alapján.

- [Azure Policy parancssori felülettel](/cli/azure/policy)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti a hálózati erőforrás-konfigurációkat, és felderítheti az Azure Kubernetes Service (ak) fürtökhöz kapcsolódó hálózati erőforrások változásait. 

Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják A AzureContainerService felhasználó összes bejegyzése platform-műveletként van naplózva. 

Azure Monitor naplók segítségével engedélyezheti és lekérdezheti a naplókat a Master Components, a Kube-apiserver és a Kube-Controller-Manager használatával. Hozza létre és kezelje a kubelet futtató csomópontokat a Container Runtime használatával, és telepítse az alkalmazásaikat a felügyelt Kubernetes API-kiszolgálón. 

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

- [A Kubernetes főcsomópont-naplóinak engedélyezése és áttekintése az Azure Kubernetes Service-ben (AKS)](view-master-logs.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) csomópontjai a NTP.Ubuntu.com-t használják az időszinkronizáláshoz, valamint a 123-es és a Network Time Protocol (NTP) UDP-porthoz. 

Ha egyéni DNS-kiszolgálókat használ, győződjön meg arról, hogy az NTP-kiszolgálók elérhetők a fürtcsomópontok számára. 

- [Az NTP-tartomány és a portokra vonatkozó követelmények ismertetése az AK-fürtcsomópontok esetében](limit-egress-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a naplók engedélyezése az Azure Kubernetes Services (ak) Master Components, a Kube-apiserver és a Kube-Controller-Manager használatával, amelyek felügyelt szolgáltatásként vannak megadva. 

• Kube-auditaksService: a vezérlő síkja művelethez tartozó megjelenítendő név (a hcpService) 

• MasterClient: a MasterClientCertificate naplójának megjelenített neve, az az AK Get-hitelesítő adatokkal rendelkező tanúsítvány 

• nodeclient: a ClientCertificate megjelenítendő neve, amelyet az ügynök csomópontjai használnak

Más naplók (például a Kube-audit) engedélyezése is. 

Ezeket a naplókat Log Analytics vagy egy másik tárolási platformra exportálhatja. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

A szervezeti üzleti követelmények alapján engedélyezheti és elküldheti ezeket az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek.

- [Tekintse át a naplózási sémát, beleértve a naplózási szerepköröket](view-master-logs.md)

- [A tárolók Azure Monitor megismerése](../azure-monitor/containers/container-insights-overview.md)

- [Azure Monitor engedélyezése tárolók számára](../azure-monitor/containers/container-insights-onboard.md)

- [A Kubernetes főcsomópont-naplóinak engedélyezése és áttekintése az Azure Kubernetes Service-ben (AKS)](view-master-logs.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure Kubernetes Service (ak) erőforrásain végrehajtott műveletek figyelése a tevékenység-naplók használatával az összes tevékenység és állapotuk megtekintéséhez. Határozza meg, hogy milyen műveleteket végeztek az előfizetésben lévő erőforrásokon a tevékenység naplói: ki kezdte el a műveletet.

a művelet bekövetkeztekor

a művelet állapota

más tulajdonságok értékei, amelyek segíthetnek a művelet megkutatásában

A Azure PowerShellon, az Azure parancssori felületen (CLI), az Azure REST API vagy a Azure Portalon keresztül kérhet le adatokat a tevékenység naplóból. 

Engedélyezze a naplókat az AK fő összetevőin, például: 

• Kube-auditaksService: a vezérlő síkja művelethez tartozó megjelenítendő név (a hcpService) 

• MasterClient: a MasterClientCertificate naplójának megjelenített neve, az az AK Get-hitelesítő adatokkal rendelkező tanúsítvány 

• nodeclient: a ClientCertificate megjelenítendő neve, amelyet az ügynök csomópontjai használnak

Más naplók, például a Kube-audit bekapcsolása. 

- [A Kubernetes Master Node-naplók engedélyezése és áttekintése az AK-ban](view-master-logs.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: log Analytics ügynökök automatikus telepítésének engedélyezése az AK-fürtcsomópontok adatainak gyűjtéséhez. Emellett az Azure Log Analytics monitorozási ügynök automatikus kiépítés az Azure Security Centerról, alapértelmezés szerint az automatikus kiépítés ki van kapcsolva. Az ügynök manuálisan is telepíthető. Az automatikus kiépítés után a Security Center telepíti az Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépen és a létrehozott újakon. A Security Center adatokat gyűjt az Azure Virtual Machines (VM), a virtuálisgép-méretezési csoportok és a IaaS-tárolók (például a Kubernetes) számára a biztonsági rések és fenyegetések figyelése érdekében. Az adatok gyűjtése az Azure Log Analytics Agent használatával történik, amely különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat olvas be a gépről, és az adatokat a munkaterületre másolja az elemzéshez. 

Az adatgyűjtés szükséges a hiányzó frissítések láthatóságának biztosításához, a helytelenül konfigurált operációsrendszer-biztonsági beállítások, az Endpoint Protection állapota, valamint az állapot és a fenyegetések észlelése érdekében.

- [Az log Analytics-ügynök automatikus kiépítés engedélyezése](../security-center/security-center-enable-data-collection.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

Útmutató: az Azure Kubernetes szolgáltatás (ak) példányainak **Bevezetése** Azure monitor és a megfelelő Azure log Analytics munkaterület megőrzési időszakának beállítása a szervezet megfelelőségi követelményeinek megfelelően. 

- [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

Útmutató: az Azure Kubernetes szolgáltatás (ak) példányainak **bevezetése** a fürthöz tartozó diagnosztikai beállítások Azure monitor és konfigurálásához. 

Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon. Azure Monitor naplók engedélyezve vannak és kezelhetők a Azure Portalban vagy a CLI-n keresztül, és a Kubernetes szerepköralapú hozzáférés-vezérléssel (Kubernetes RBAC), az Azure RBAC és a nem RBAC engedélyezett AK-fürtökkel egyaránt működnek.

Tekintse meg az AK Master Components (Kube-apiserver és Kube-controllermanager) által létrehozott naplókat az alkalmazás és a szolgáltatások hibaelhárításához. Az Azure Sentinel vagy egy harmadik féltől származó, a központi naplók felügyeletéhez és monitorozásához szükséges, illetve az azokra vonatkozó adatkezelési funkciók engedélyezése.

- [A Kubernetes Master Node-naplók engedélyezése és áttekintése az AK-ban](view-master-logs.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) és a Security Center együttes használata az AK-csomópontok mélyebb láthatóságának megszerzéséhez. Tekintse át Security Center riasztásokat a gazdagépen és a fürt szintjén észlelt fenyegetésekkel és rosszindulatú tevékenységekkel kapcsolatban. A Security Center a nyers biztonsági események folyamatos elemzését hajtja végre egy AK-fürtben, például a hálózati adataiban, a folyamatok létrehozásában és a Kubernetes-naplóban. Állapítsa meg, hogy a tevékenység várható viselkedés-e, vagy hogy az alkalmazás nem működik-e. A Azure Monitor metrikák és naplók használata az eredmények alátámasztására. 

- [Az Azure Kubernetes Services és a Security Center integrációjának megismerése](../security-center/defender-for-kubernetes-introduction.md)

- [A standard szintű Azure Security Center engedélyezése](../security-center/security-center-get-started.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: az Azure-hoz készült Microsoft anti-malware telepítése és engedélyezése az Azure Kubernetes szolgáltatás (ak) virtuális gépei és virtuálisgép-méretezési csoport csomópontjai számára. A szervizeléssel kapcsolatos riasztások áttekintése Security Center.

- [Microsoft antimalware az Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

- [Biztonsági riasztások hivatkozási útmutatója](../security-center/alerts-reference.md)

- [Tárolók riasztásai – Azure Kubernetes Service-fürtök](../security-center/alerts-reference.md#alerts-akscluster)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: az Azure Kubernetes Service (ak) a CoreDNS-projektet használja a fürt DNS-kezeléséhez és megoldásához.

A DNS-lekérdezések naplózásának engedélyezése a dokumentált konfiguráció alkalmazásával a coredns – egyéni ConfigMap. 

- [A CoreDNS testreszabása Azure Kubernetes Service-szel](coredns-custom.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: a kubectl, egy parancssori ügyfél, az Azure Kubernetes szolgáltatás (ak) használatával kezelheti a Kubernetes-fürtöket, és lekérheti a naplókat az AK-ból a hibaelhárítási célból. A Kubectl már telepítve van, ha Azure Cloud Shell használ. A kubectl helyi telepítéséhez használja az "install-AzAksKubectl" parancsmagot.

- [Rövid útmutató – Azure Kubernetes Service-fürt üzembe helyezése a PowerShell használatával](kubernetes-walkthrough-powershell.md)

- [Kubelet-naplók lekérése Azure Kubernetes Service- (AKS-) fürtcsomópontokból](kubelet-logs.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: maga az Azure Kubernetes szolgáltatás (ak) nem biztosít olyan Identitáskezelés-kezelési megoldást, amely normál felhasználói fiókokat és jelszavakat tárol. A Azure Active Directory-(Azure AD-) integrációval a felhasználók vagy csoportok hozzáférést biztosíthatnak a Kubernetes-erőforrásokhoz a névtérben vagy a fürtön belül. 

Ad hoc lekérdezések végrehajtása az AK-beli felügyeleti csoportokba tartozó fiókok felderítéséhez az Azure AD PowerShell-modullal

Az Azure CLI használata olyan műveletekhez, mint például a "hozzáférési hitelesítő adatok beszerzése egy felügyelt Kubernetes-fürthöz", a hozzáférés rendszeres egyeztetésének elősegítése érdekében. Ennek a folyamatnak a végrehajtásával megőrizheti a szolgáltatásfiókok frissített leltárát, amelyek egy másik elsődleges felhasználói típus az AK-ban. Security Center identitás-és hozzáférés-kezelési javaslatainak betartatása.

- [Az AK integrálása az Azure AD-vel](./azure-ad-integration-cli.md)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Identitás és hozzáférés figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) nem rendelkezik a gyakori alapértelmezett jelszavakkal, és nem biztosít olyan Identitáskezelés-kezelési megoldást, amelyben normál felhasználói fiókokat és jelszavakat lehet tárolni. A Azure Active Directory (Azure AD) integrációval szerepköralapú hozzáférést biztosíthat az AK-erőforrásokhoz a névtérben vagy a fürtön belül. 

Ad hoc lekérdezések végrehajtása az AK-beli felügyeleti csoportokba tartozó fiókok felderítéséhez az Azure AD PowerShell-modullal

- [Az AK hozzáférési és identitási beállításainak megismerése](concepts-identity.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: a felhasználói hitelesítés integrálása az Azure Kubernetes Service (ak) fürtökhöz Azure Active Directory (Azure ad) használatával. Jelentkezzen be egy AK-fürtbe egy Azure AD-hitelesítési jogkivonat használatával. Konfigurálja a Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC) a Kubernetes-konfigurációs (kubeconfig) információkhoz, a névterekhez és a fürterőforrások kezeléséhez szükséges rendszergazdai hozzáféréshez. 

Szabályzatokat és eljárásokat hozhat létre a dedikált rendszergazdai fiókok használata körül. Security Center identitás-és hozzáférés-kezelési javaslatok implementálása.

- [Identitás és hozzáférés figyelése Azure Security Center](../security-center/security-center-identity-access.md)

- [Fürt erőforrásaihoz való hozzáférés szabályozása](azure-ad-rbac.md)

- [Azure szerepköralapú hozzáférés-vezérlés használata](control-kubeconfig-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: egyszeri bejelentkezés használata az Azure Kubernetes Service (ak) szolgáltatáshoz Azure Active Directory (Azure ad) integrált hitelesítéssel egy AK-fürthöz.

- [A Kubernetes-naplók,-események és a pod-metrikák valós idejű megtekintése](../azure-monitor/containers/container-insights-livedata-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) hitelesítésének integrálása Azure Active Directory (Azure ad) használatával. 

Engedélyezze az Azure AD Multi-Factor Authentication (MFA) használatát, és kövesse Security Center identitás-és hozzáférés-kezelési javaslatait.

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md) 

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: a megadott Azure Kubernetes szolgáltatásbeli (ak-) fürtökre és kapcsolódó erőforrásokra való bejelentkezéshez konfigurált, multi-Factor Authentication (MFA) jogosultságú hozzáférési munkaállomás (Paw) használata.
- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory (Azure ad) biztonsági jelentések használata az Azure ad-hez integrált Azure Kubernetes szolgáltatással (ak). Riasztások akkor hozhatók létre, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: csak a jóváhagyott helyekről származó Azure-erőforrások kezelése

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel lehetővé teheti az Azure Kubernetes Service-(ak-) fürtök elérését az IP-címtartományok, illetve országok/régiók számára. Ehhez integrált hitelesítés szükséges a Azure Active Directory (Azure AD)-vel rendelkező AK-hoz.

Korlátozza az AK API-kiszolgáló hozzáférését az IP-címtartományok korlátozott készletéről, mivel a fürt műveleteinek elvégzésére irányuló kéréseket kap az erőforrások létrehozásához vagy a csomópontok számának skálázásához. 

- [Biztonságos hozzáférés az API-kiszolgálóhoz a jogosult IP-címtartományok használatával az Azure Kubernetes szolgáltatásban (ak)](api-server-authorized-ip-ranges.md)

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként az Azure Kubernetes szolgáltatáshoz (ak). Az Azure AD az adatok védelme érdekében erős titkosítást használ az inaktív adatok, valamint az átvitel és a sók, a kivonatok és a biztonságos tárolók felhasználói hitelesítő adatainak használatával.

Az AK beépített szerepköreinek használata az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) – erőforrás-házirend közreműködője és tulajdonosa, házirend-hozzárendelési műveletek a Kubernetes-fürthöz

- [Azure Policy áttekintése](../governance/policy/overview.md)

- [Az Azure AD integrálása az AK-val](./azure-ad-integration-cli.md) 

- [Az AK által felügyelt Azure AD integrálása](managed-aad.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Azure Active Directory (Azure ad) biztonsági jelentések használata az Azure ad-hez integrált Azure Kubernetes szolgáltatással (ak). Az elavult fiókok felderítéséhez keressen Azure AD-naplókat. 

Az Azure Identity hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A Security Center identitás-és hozzáférési javaslatainak szervizelése.

Vegye figyelembe a támogatási és hibaelhárítási célokra használt szerepköröket. Például a Microsoft támogatási szolgálata (felhasználói beleegyezike) által végrehajtott összes művelet egy beépített Kubernetes "Edit" (Szerkesztés) szerepkörrel rendelkezik, amely az AK-support-rolebinding nevet adja. Az AK-támogatás engedélyezve van ezzel a szerepkörrel a fürt konfigurációjának és erőforrásainak szerkesztéséhez a fürtökkel kapcsolatos problémák elhárítása és diagnosztizálása érdekében. Ez a szerepkör azonban nem módosíthatja az engedélyeket, és nem hozhat létre szerepköröket vagy szerepkör-kötéseket. Ez a szerepkör-hozzáférés csak az aktív támogatási jegyek esetében engedélyezett az igény szerinti (JIT) hozzáféréssel.
 
- [Hozzáférési és identitás-beállítások az Azure Kubernetes Service (AKS) szolgáltatáshoz](concepts-identity.md)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [A felhasználó identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) felhasználói hitelesítésének integrálása Azure Active Directory (Azure ad) használatával. Diagnosztikai beállítások létrehozása az Azure AD-hez, a naplózási és bejelentkezési naplók elküldése egy Azure Log Analytics-munkaterületre. A kívánt riasztások konfigurálása (például ha egy inaktivált fiók megpróbál bejelentkezni) egy Azure Log Analytics-munkaterületen belül.
- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../azure-monitor/alerts/alerts-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) felhasználói hitelesítésének integrálása Azure Active Directory (Azure ad) használatával. Az Azure AD kockázati észlelései és Identity Protection szolgáltatásával automatizált válaszokat állíthat be a felhasználói identitásokkal kapcsolatos gyanús műveletekre. Az üzleti igények alapján további vizsgálatokat végezhet az Azure Sentinelbe.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem alkalmazható az Azure Kubernetes Service (ak) szolgáltatásra, mert Ügyfélszéf nem támogatja.
- [Ügyfélszéf támogatott szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](../security/benchmarks/security-control-data-protection.md)című témakörben talál.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: az Azure Kubernetes Service (ak) környezetekhez kapcsolódó erőforrásokon használható címkék használata a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követéséhez.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [A felügyelt fürtökhöz tartozó címkék frissítése](/rest/api/aks/managedclusters/updatetags)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: a csapatok és munkaterhelések logikai elkülönítése ugyanabban a fürtben az Azure Kubernetes szolgáltatással (ak), hogy a lehető legkevesebb jogosultságot biztosítson az egyes csapatok által igényelt erőforrásokra. 

Logikai elkülönítési határ létrehozásához használja a Kubernetes névtérét. Érdemes lehet további Kubernetes funkciókat létrehozni az elkülönítéshez és a több-bérlőhöz, például az ütemezéshez, a hálózatkezeléshez, a hitelesítéshez/engedélyezéshez és a tárolóhoz.

Különböző előfizetések és/vagy felügyeleti csoportok implementálása fejlesztési, tesztelési és éles környezetekben. Különálló AK-fürtök hálózatkezeléssel, ha azokat különálló virtuális hálózatokra telepíti, amelyek megfelelő címkével rendelkeznek.

- [További információ a fürtök elkülönítésével kapcsolatos ajánlott eljárásokról az AK-ban](operator-best-practices-cluster-isolation.md)

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A hálózati kapcsolat és a biztonság ajánlott eljárásainak ismertetése az AK-ban](operator-best-practices-network.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: külső gyártótól származó megoldás használata az Azure Marketplace-en olyan hálózati peremhálózaton, amely figyeli a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztást küld az információs biztonsági szakembereknek.

A Microsoft kezeli a mögöttes platformot, és az összes vásárlói tartalmat bizalmasként kezeli, és nagy hosszúságú időt biztosít az ügyfelek adatvesztésének és a kitettségnek a védelme érdekében. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az AK-funkciókhoz szükséges portok, címek és tartománynevek listája](limit-egress-traffic.md)

- [A Azure Firewall diagnosztikai beállításainak konfigurálása](../firewall/firewall-diagnostics.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: hozzon létre egy https bejövő adatkezelőt, és használjon saját TLS-tanúsítványokat (vagy opcionálisan titkosítjuk) az Azure Kubernetes szolgáltatás (ak) üzembe helyezéséhez. 

A Kubernetes kimenő forgalmának alapértelmezett titkosítása HTTPS/TLS protokollon keresztül történik. Tekintse át a potenciálisan nem titkosított kimenő forgalmat az AK-példányokból további figyelés céljából. Ilyen lehet például az NTP-forgalom, a DNS-forgalom, a frissítések beolvasására szolgáló HTTP-forgalom bizonyos esetekben. 

- [HTTPS bemenő vezérlő létrehozása az AK-ban és saját TLS-tanúsítványok használata](ingress-own-tls.md)

- [HTTPS bemenő adatvezérlő létrehozása az AK-ban a let titkosítással](ingress-tls.md)

- [Az AK által használt lehetséges kimenő portok és protokollok listája](limit-egress-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.
A Microsoft kezeli a mögöttes platformot, és az összes vásárlói tartalmat bizalmasként kezeli, és nagy hosszúságú időt biztosít az ügyfelek adatvesztésének és a kitettségnek a védelme érdekében. 

Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: a Azure Resource Manager beépített Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) engedélyezési rendszer használatával biztosíthatja az Azure-erőforrások részletes hozzáférés-kezelését.

Konfigurálja az Azure Kubernetes Service (ak) szolgáltatást a Azure Active Directory (Azure AD) használatára a felhasználói hitelesítéshez. Jelentkezzen be egy AK-fürtbe az Azure AD hitelesítési token használatával ezzel a konfigurációval. 

Az AK beépített szerepköreinek használata az Azure RBAC-erőforrás-házirenddel közreműködővel és tulajdonossal, házirend-hozzárendelési műveletek az AK-fürthöz

- [Fürt erőforrásaihoz való hozzáférés szabályozása az Azure RBAC és az Azure AD-identitások használatával az AK-ban](azure-ad-rbac.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.
A Microsoft kezeli a mögöttes platformot, és az összes vásárlói tartalmat bizalmasként kezeli, és nagy hosszúságú időt biztosít az ügyfelek adatvesztésének és a kitettségnek a védelme érdekében. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az Azure Kubernetes szolgáltatásban (ak) lévő kötetekhez megadott két elsődleges tárolási típust Azure-lemezek vagy Azure filesek. Mindkét típusú tároló az Azure Storage Service Encryption-t (SSE) használja, amely a biztonság növelése érdekében titkosítja az inaktív adatok mennyiségét. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik.

Az ügyfél által felügyelt kulcsokkal titkosítható a REST-alapú adatlemezek mind az operációs rendszer, mind az adatlemezek a titkosítási kulcsok további vezérléséhez. Az ügyfelek feladata a kulcsfontosságú felügyeleti tevékenységek, például a kulcsok biztonsági mentése és elforgatása. A lemezek jelenleg nem titkosíthatók Azure Disk Encryption használatával az AK csomópont szintjén.

- [Ajánlott eljárások a tároláshoz és a biztonsági mentésekhez az Azure Kubernetes szolgáltatásban (ak)](operator-best-practices-storage.md)

- [Saját kulcsok (BYOK) használata Azure-lemezekkel az Azure Kubernetes szolgáltatásban (ak)](azure-disk-customer-managed-keys.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: az Azure Kubernetes szolgáltatásban (ak) üzemeltetett felügyelt Kubernetes-fürtökön üzembe helyezett tároló-munkaterhelések teljesítményének figyelése a tárolók Azure monitor használatával. 

Riasztások konfigurálása a proaktív értesítésekhez vagy a naplók létrehozásához, ha a processzor és a memória kihasználtsága a csomópontokon vagy tárolókban meghaladja a meghatározott küszöbértékeket, vagy ha a fürt állapota megváltozik az infrastruktúra vagy a csomópontok állapota összesítésben. 

Az Azure-tevékenység naplójának használatával magas szinten figyelheti az AK-fürtöket és a kapcsolódó erőforrásokat. A Prometheus-nal integrálva megtekintheti a csomópontokból és Kubernetes származó alkalmazások és munkaterhelések mérőszámait, és lekérdezésekkel egyéni riasztásokat, irányítópultokat és részletes elemzéseket készíthet.

- [A tárolók Azure Monitor megismerése](../azure-monitor/containers/container-insights-overview.md)

- [Azure Monitor engedélyezése tárolók számára](../azure-monitor/containers/container-insights-onboard.md)

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: az Security Center segítségével figyelje a biztonsági rések Azure Container Registryét, beleértve az Azure Kubernetes Service (ak)-példányokat. Engedélyezze a tároló-beállításjegyzékek kötegét Security Center annak biztosításához, hogy Security Center készen álljon a beállításjegyzékbe leküldhető lemezképek vizsgálatára.

Értesítést kaphat a Security Center irányítópulton, ha a rendszer problémát észlel, miután Security Center megvizsgálja a rendszerképet a Qualys használatával. A Container nyilvántartói csomag funkciója mélyebb láthatóságot biztosít a Azure Resource Manager-alapú nyilvántartásokban használt rendszerképek sebezhetőségei között. 

Minden biztonsági rés esetében használható a Security Center a gyakorlatban alkalmazható ajánlásokhoz. Ezek a javaslatok súlyossági besorolást és szervizelési útmutatást tartalmaznak. 

- [Ajánlott eljárások a tárolók rendszerképének kezeléséhez és biztonságához az Azure Kubernetes szolgáltatásban (ak)](../security-center/defender-for-container-registries-introduction.md)

- [A tárolók képkezelésének és biztonságának ajánlott eljárásainak ismertetése az AK-ban](operator-best-practices-container-image-management.md)

- [A tároló-beállításjegyzék és a Azure Security Center közötti integráció ismertetése](../security-center/defender-for-container-registries-introduction.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: a rendszer automatikusan alkalmazza a biztonsági frissítéseket a Linux-csomópontokra az ügyfél Azure Kubernetes-szolgáltatás (ak) fürtjének védelme érdekében. Ezek a frissítések operációsrendszer-biztonsági javításokat vagy kernel-frissítéseket tartalmaznak. 

Vegye figyelembe, hogy a Windows Server-csomópontok naprakészen tartásának folyamata eltér a Linux rendszerű csomópontok, mivel a Windows Server-csomópontok nem kapják meg a napi frissítéseket. Ehelyett az ügyfeleknek frissíteniük kell a Windows Server Node-készletekben az AK-fürtökön, amely új csomópontokat helyez üzembe az Azure Vezérlőpult vagy az Azure CLI használatával a legújabb alapszintű ablak-kiszolgáló lemezképével és javításával. Ezek a frissítések biztonsági vagy funkcionalitásbeli újdonságokat tartalmaznak az AK-ra vonatkozóan.

- [Ismerje meg, hogy a frissítések hogyan lesznek alkalmazva a Linux rendszerű AK-fürtcsomópontok esetében](node-updates-kured.md)

- [AK-alapú csomópont-készlet frissítése Windows Server-csomópontokat használó AK-fürtökhöz](use-multiple-node-pools.md#upgrade-a-node-pool)

- [Azure Kubernetes szolgáltatás (ak) csomópont-rendszerképének frissítése](node-image-upgrade.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: automatikus javítási megoldás üzembe helyezése harmadik féltől származó szoftverek címeihez

**Útmutató**: manuális folyamat megvalósítása annak biztosítására, hogy az Azure Kubernetes szolgáltatás (ak) fürtcsomópont külső alkalmazásai a fürt élettartama alatt is megmaradjanak. Ehhez szükség lehet az automatikus frissítések engedélyezésére, a csomópontok figyelésére vagy rendszeres újraindításra.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: az Security Center-vizsgálati eredmények konzisztens időközönként történő exportálása és az eredmények összehasonlítása annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. 

A "Get-AzSecurityTask" PowerShell-parancsmaggal automatizálhatja azon biztonsági feladatok beolvasását, amelyeket a Security Center javasol a végrehajtásához, hogy megerősítse a biztonsági helyzetek és a biztonsági rések vizsgálatának eredményeit.

- [A Azure Security Center által felderített sebezhetőségek megtekintése a PowerShell használatával](/powershell/module/az.security/get-azsecuritytask)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Security Center által megadott súlyossági minősítést a biztonsági rések szervizelésének rangsorolására használhatja. 

Az Azure által kínált beépített sebezhetőségi felmérési eszközt (például Qualys vagy Rapid7) használja a gyakori sebezhetőségi pontozási rendszer (CVSS) (vagy a vizsgálati eszköz által nyújtott más pontozási rendszerek) használata esetén.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetéseken belüli összes erőforrást (például a számítási, tárolási, hálózati stb.). Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott Azure Resource Manager-alapú erőforrások létrehozását és használatát.

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra metaadatok használatával, hogy logikailag szervezze őket a besorolásba.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. 

Az Azure Kubernetes-szolgáltatás (ak) csomópont-készletének létrehozásakor a rendszer megfertőzi, címkézi vagy címkéket alkalmaz. A csomóponton belüli összes csomópont örökli a megromlást, a címkét vagy a címkét is.

A beszennyező anyagok, címkék vagy címkék segítségével rendszeres időközönként összeegyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrásokat időben törölje az előfizetésből.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és a felhasználók számára](../azure-resource-manager/management/tag-resources.md)

- [Felügyelt fürtök – címkék frissítése](/rest/api/aks/managedclusters/updatetags)

- [Válassza ki a csomópont-készlethez tartozó Taint, címkét vagy címkét](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: határozza meg a jóváhagyott Azure-erőforrások és jóváhagyott szoftverek listáját a számítási erőforrások számára a szervezeti üzleti igények alapján.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
-   Nem engedélyezett erőforrástípusok 

-   Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésében lévő erőforrásokat. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrást a szervezeti üzleti követelmények alapján jóváhagyja.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: a környezetben telepített szoftverek megkereséséhez használja a Azure Automation Change Tracking és a leltári funkciókat. 

Összegyűjtheti és megtekintheti a szoftverek, a fájlok, a Linux-démonok, a Windows-szolgáltatások és a Windows-beállításkulcsok leltárát a számítógépeken, és figyelheti a nem jóváhagyott szoftveres alkalmazásokat. 

Nyomon követheti a gépek konfigurációit, hogy segítséget nyújtsanak a működési problémák azonosításában a környezetében, és jobban megértse a gépek állapotát.

- [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: a környezetben telepített szoftverek megkereséséhez használja a Azure Automation Change Tracking és a leltári funkciókat. 

Összegyűjtheti és megtekintheti a szoftverek, a fájlok, a Linux-démonok, a Windows-szolgáltatások és a Windows-beállításkulcsok leltárát a számítógépeken, és figyelheti a nem jóváhagyott szoftveres alkalmazásokat. 

Nyomon követheti a gépek konfigurációit, hogy segítséget nyújtsanak a működési problémák azonosításában a környezetében, és jobban megértse a gépek állapotát.

- [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

- [A fájl integritás-figyelésének használata](../security-center/security-center-file-integrity-monitoring.md)

- [Az Azure Change Tracking ismertetése](../automation/change-tracking/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: a környezetben telepített szoftverek megkereséséhez használja a Azure Automation Change Tracking és a leltári funkciókat. 

Összegyűjtheti és megtekintheti a szoftverek, a fájlok, a Linux-démonok, a Windows-szolgáltatások és a Windows-beállításkulcsok leltárát a számítógépeken, és figyelheti a nem jóváhagyott szoftveres alkalmazásokat. 

Nyomon követheti a gépek konfigurációit, hogy segítséget nyújtsanak a működési problémák azonosításában a környezetében, és jobban megértse a gépek állapotát.

Az adaptív alkalmazások elemzésének engedélyezése Security Centerban olyan alkalmazások esetében, amelyek már léteznek a környezetben.

- [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

 
Azure Security Center adaptív alkalmazás használata
- [Vezérlők](../security-center/security-center-adaptive-application.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésében lévő erőforrásokat. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben hozhatók létre a beépített szabályzat-definíciók használatával.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.
- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokban

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) nem rendelkezik olyan identitáskezelési megoldással, amelyben normál felhasználói fiókokat és jelszavakat tárolnak. Ehelyett használja a Azure Active Directory (Azure AD) szolgáltatást integrált identitási megoldásként az AK-fürtökhöz. 

A felhasználók vagy csoportok számára hozzáférést biztosíthat a névtérben vagy a fürtön belüli Kubernetes-erőforrásokhoz az Azure AD-integráció használatával. 

Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre olyan fiókok felderítéséhez, amelyek az AK-beli felügyeleti csoportok tagjai, és amelyeket a hozzáférés rendszeres egyeztetéséhez használhat. Használja az Azure CLI-t olyan műveletekhez, mint például a "hozzáférési hitelesítő adatok lekérése egy felügyelt Kubernetes-fürthöz. Security Center identitás-és hozzáférés-kezelési javaslatok implementálása.

- [Az AK kezelése az Azure CLI-vel](/cli/azure/aks)

- [Az AK és az Azure AD integrációjának megismerése](concepts-identity.md)

- [Az AK integrálása az Azure AD-vel](./azure-ad-integration-cli.md)

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Identitás és hozzáférés figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) funkcióinak használatával logikailag elkülönítheti a csoportokat és a munkaterheléseket ugyanabban a fürtben a lehető legkevesebb jogosultsággal, az egyes csapatok által igényelt erőforrásokra kiterjedően. 

A Kubernetes névtér implementálása logikai elkülönítési határ létrehozásához. Használjon Azure Policy aliasokat a "Microsoft. Tárolószolgáltatás" névtérben egyéni szabályzatok létrehozásához az Azure Kubernetes szolgáltatás (ak) példányainak konfigurálásához vagy érvényesítéséhez. 

Tekintse át és implementálja az elkülönítésre és a több-bérlőre vonatkozó további Kubernetes funkciókat, amelyek az alábbiakat tartalmazzák: ütemezés, hálózatkezelés, hitelesítés/engedélyezés és tárolók. Külön előfizetéseket és/vagy felügyeleti csoportokat is használhat a fejlesztési, tesztelési és éles környezetekhez. Különálló AK-fürtöket virtuális hálózatokkal, a megfelelő címkével rendelkező alhálózatokkal és a webalkalmazási tűzfallal (WAF) védett alhálózatokkal.

- [További információ a fürtök elkülönítésével kapcsolatos ajánlott eljárásokról az AK-ban](operator-best-practices-cluster-isolation.md)

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [A hálózati kapcsolat és a biztonság ajánlott eljárásainak ismertetése az AK-ban](operator-best-practices-network.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. tárolószolgáltatás" névtérben egyéni szabályzatok létrehozásához az Azure Kubernetes szolgáltatásbeli (ak) példányok konfigurációjának naplózásához vagy érvényesítéséhez. Beépített Azure Policy-definíciókat használhat.

Többek között a következő beépített szabályzat-definíciók tartoznak ide:

• HTTPS bejövő forgalom kikényszerítve a Kubernetes-fürtben

• A Kubernetes-szolgáltatásokban meg kell határozni a jóváhagyott IP-tartományokat

• Szerepköralapú hozzáférés-vezérlést (RBAC) kell használni a Kubernetes-szolgáltatásokban

• Csak a Kubernetes-fürtön engedélyezett tároló lemezképek biztosítása

Exportálja az AK-konfiguráció sablonját JavaScript Object Notation (JSON) Azure Resource Manager. Rendszeres időközönként ellenőrizze, hogy a konfigurációk megfelelnek-e a szervezet biztonsági követelményeinek. Az Azure Security Center ajánlásai az Azure-erőforrások biztonságos alapkonfigurációja használhatók. 

- [Az AK Pod biztonsági szabályzatok konfigurálása és kezelése](use-pod-security-policies.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: az Azure Kubernetes-FÜRTÖK (ak-beli) fürtök üzembe helyezése a biztonságos operációs rendszert futtató virtuális gépeken történik. A gazdagép operációs rendszerének további biztonsági korlátozási lépései vannak beépítve a támadás felszínének csökkentése érdekében, és lehetővé teszi a tárolók biztonságos módon történő üzembe helyezését. 

Az Azure a napi javításokat (beleértve a biztonsági javításokat is) az AK-beli virtuálisgép-gazdagépekre alkalmazza, amelyek újraindítást igényelnek. Az AK-beli virtuális gépek gazdagépei igény szerinti újraindítását az ügyfelek felelősek. 

- [Biztonság megerősítése az AK-ügynök Node gazda operációs rendszeréhez](security-hardened-vm-host-image.md)

- [A biztonság megerősítése az AK-beli virtuális gépek gazdagépei között](security-hardened-vm-host-image.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Kubernetes szolgáltatás (ak) fürt biztonságossá tétele a pod biztonsági házirendekkel.  Korlátozza a hüvelyek ütemezését a fürt biztonságának javítása érdekében. 

A nem engedélyezett erőforrásokat kérő hüvelyek nem futhatnak az AK-fürtben. 

A Azure Policy [megtagadás] és a [telepítés, ha nem létezik] hatással van az AK-beli üzemelő példányokhoz kapcsolódó Azure-erőforrások (például virtuális hálózatok, alhálózatok, Azure-tűzfalak, Storage-fiókok stb.) biztonságos beállításainak betartatására. 

Hozzon létre egyéni Azure Policy-definíciókat a következő névterek aliasai használatával: 

• Microsoft. Tárolószolgáltatás

• Microsoft. Network

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: az Azure Kubernetes Service-(ak-) fürtöket a gazdagép virtuális gépei helyezik üzembe egy biztonsági optimalizált operációs rendszerrel. A gazdagép operációs rendszerének további biztonsági korlátozási lépései vannak beépítve a támadás felszínének csökkentése érdekében, és lehetővé teszi a tárolók biztonságos módon történő üzembe helyezését. 

Tekintse meg a gazdagép operációs rendszerbe beépített Internet Security (CIS) vezérlők listáját.  

- [Biztonság megerősítése az AK-ügynök Node gazda operációs rendszeréhez](security-hardened-vm-host-image.md)

- [Az AK-fürtök állapot-konfigurációjának ismertetése](concepts-clusters-workloads.md#control-plane)

- [A biztonság megerősítése az AK-beli virtuális gépek gazdagépei között](security-hardened-vm-host-image.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure Repos használatával biztonságosan tárolhatja és kezelheti a konfigurációkat, ha egyéni Azure Policy-definíciókat használ. Exportálja az Azure Kubernetes szolgáltatás (ak) konfigurációjának sablonját JavaScript Object Notation (JSON)-ben Azure Resource Manager. Rendszeresen ellenőrizze, hogy a konfigurációk megfelelnek-e a szervezet biztonsági követelményeinek. 

Külső megoldások, például a Terraform megvalósítása olyan konfigurációs fájl létrehozásához, amely deklarálja a Kubernetes-fürt erőforrásait. Az AK-beli üzemelő példányok megerősíthető az ajánlott biztonsági eljárások megvalósítása és a konfiguráció kódként való tárolása biztonságos helyen.

- [Kubernetes-fürt meghatározása](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

Biztonság megerősítése az AK-ügynök Node gazda operációs rendszeréhez

security-hardened-vm-host-image.md

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható az Azure Kubernetes Service (ak) szolgáltatásra. Az KABAi szolgáltatás alapértelmezés szerint a biztonsági optimalizált gazdagép operációs rendszerét (OS) biztosítja. Egy alternatív vagy egyéni operációs rendszer nem választható ki aktuális lehetőségként.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben hozhatók létre a beépített szabályzat-definíciókkal, valamint a "Microsoft. tárolószolgáltatás" névtérben Azure Policy aliasokkal. 

Egyéni szabályzatokat hozhat létre az auditáláshoz és a rendszerkonfigurációk érvényesítéséhez. Dolgozzon ki egy folyamatot és folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: az Azure Kubernetes Service-(ak-) fürtöket a gazdagép virtuális gépei helyezik üzembe egy biztonsági optimalizált operációs rendszerrel. A gazdagép operációs rendszerének további biztonsági korlátozási lépései vannak beépítve a támadás felszínének csökkentése érdekében, és lehetővé teszi a tárolók biztonságos módon történő üzembe helyezését. 

Tekintse meg az AK-gazdagépekre épülő, az Internet Security (CIS) által használt központ-vezérlők listáját.  

- [Biztonság megerősítése az AK-ügynök Node gazda operációs rendszeréhez](security-hardened-vm-host-image.md)

- [A biztonság megerősítése az AK-beli virtuális gépek gazdagépei között](security-hardened-vm-host-image.md)

- [Az AK-fürtök állapot-konfigurációjának ismertetése](concepts-clusters-workloads.md#control-plane)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: az Azure Kubernetes szolgáltatással (ak) üzemelő példányokhoz kapcsolódó erőforrások alapkonfigurációjának vizsgálatához használja a Security Center. A példákban szereplő erőforrások közé tartoznak a következők: de nem korlátozódnak az AK-fürtre, a virtuális hálózat, ahol az AK-fürt telepítve volt, a Terraform állapotának nyomon követéséhez használt Azure Storage-fiók, vagy az AK-fürt operációs rendszerének és adatlemezei titkosítási kulcsaihoz használt Azure Key Vault példányok.

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: a "számítási alkalmazások" szakaszban Security Center tárolói javaslatok használata az &amp; Azure KUBERNETES szolgáltatás (ak) fürtjének alapkonfigurációjának vizsgálatához. Értesítést kaphat a Security Center irányítópulton, ha konfigurációs problémák vagy biztonsági rések találhatók. Ehhez engedélyezni kell a nem kötelező tároló-beállításjegyzékek csomagot, amely lehetővé teszi a Security Center számára a rendszerkép vizsgálatát.  

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../security-center/container-security.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Azure Key Vault integrálása Azure Kubernetes szolgáltatással (ak) rendelkező fürttel FlexVolume-meghajtó használatával. A Azure Key Vault használatával tárolhatja és rendszeresen elforgathatja a titkokat, például a hitelesítő adatokat, a Storage-fiók kulcsait vagy a tanúsítványokat. A FlexVolume-illesztőprogram lehetővé teszi, hogy az AK-fürt natív módon beolvassa a hitelesítő adatokat a Key Vaultból, és biztonságosan biztosítsa azokat csak a kérelmező Pod számára. A pod felügyelt identitással hozzáférést igényelhet Key Vaulthoz, és lekérheti a szükséges hitelesítő adatokat a FlexVolume-illesztőprogram használatával. Győződjön meg arról, Key Vault a Soft delete engedélyezve van. 

Korlátozza a hitelesítő adatokat, mert nem határozza meg a hitelesítő adatokat az alkalmazás kódjában. 

Kerülje a rögzített vagy megosztott hitelesítő adatok használatát. 

- [Az Azure Kubernetes Service (AKS) alkalmazásainak és fürtjeinek biztonsági fogalmai](concepts-security.md)

- [A Key Vault használata az AK-fürttel](developer-best-practices-pod-security.md#limit-credential-exposure)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a hitelesítő adatok nem adhatók meg az alkalmazás kódjában biztonsági szempontból ajánlott eljárásként. Felügyelt identitások használata az Azure-erőforrásokhoz, hogy a pod hitelesítse magát az Azure bármely olyan szolgáltatásával szemben, amely támogatja azt, beleértve a Azure Key Vaultt is. A pod hozzárendel egy Azure-identitást a Azure Active Directory (Azure AD) hitelesítéséhez, és olyan digitális jogkivonatot kap, amely más Azure-szolgáltatásokhoz is bemutatható, amelyek ellenőrzi, hogy a pod jogosult-e a szolgáltatás elérésére és a szükséges műveletek elvégzésére. 

Vegye figyelembe, hogy a hüvelyben felügyelt identitások csak Linux-hüvelyek és a tárolók rendszerképeinek használatára szolgálnak. A digitális kulcsok és a hitelesítő adatok tárolásához és lekéréséhez Azure Key Vault kiépítése. A kulcsok, például az operációsrendszer-lemezek titkosításához használt kulcsok, az AK-fürt adatai Azure Key Vault tárolhatók.

Az egyszerű szolgáltatások az AK-fürtökben is használhatók. Az egyszerű szolgáltatásokat használó fürtök azonban végül olyan állapotba kerülhetnek, amelyben az egyszerű szolgáltatásnevet meg kell újítani a fürt működésének megtartása érdekében. Az egyszerű szolgáltatások kezelése bonyolultságot biztosít, ezért a felügyelt identitások könnyebben használhatók. Ugyanezek az engedélyezési követelmények érvényesek az egyszerű szolgáltatásokra és a felügyelt identitásokra is.

- [Felügyelt identitások és Key Vaultek megismerése az Azure Kubernetes szolgáltatással (ak)](developer-best-practices-pod-security.md#limit-credential-exposure)

- [Azure Active Directory Pod-identitás](https://github.com/Azure/aad-pod-identity)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat olyan biztonságos helyszínekre helyezze, mint például a Azure Key Vault

Korlátozza a hitelesítő adatokat, mert nem határozza meg a hitelesítő adatokat az alkalmazás kódjában. és ne használja a megosztott hitelesítő adatokat. A digitális kulcsok és a hitelesítő adatok tárolására és lekérésére Azure Key Vault kell használni. Felügyelt identitások használata az Azure-erőforrásokhoz, hogy a pod kérjen hozzáférést más erőforrásokhoz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Fejlesztői ajánlott eljárások a pod biztonsághoz](developer-best-practices-pod-security.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: központilag felügyelt kártevő szoftver használata

**Útmutató**: az AK kezeli az ügynök-csomópontok életciklusát és műveleteit az Ön nevében – az ügynök-csomópontokhoz társított IaaS-erőforrások módosítása nem támogatott. Linux-csomópontok esetében azonban a démon-készletekkel telepítheti az olyan egyéni szoftvereket, mint a kártevők elleni megoldás.

- [Biztonsági riasztások hivatkozási útmutatója](../security-center/alerts-reference.md)

- [Tárolók riasztásai – Azure Kubernetes Service-fürtök](../security-center/alerts-reference.md#alerts-akscluster)

- [AK megosztott felelősség és démon-készletek](support-policies.md#shared-responsibility)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: az AK-erőforrásokra feltöltött fájlok előzetes vizsgálata. A Security Center veszélyforrások észlelése az adatszolgáltatások számára a Storage-fiókokba feltöltött kártevők észleléséhez, ha Azure Storage-fiókot használ adattárként, vagy nyomon szeretné követni az AK-fürt Terraform állapotát. 

- [Az adatszolgáltatások fenyegetés-észlelésének megismerése Azure Security Center](../security-center/azure-defender.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: az AK kezeli az ügynök-csomópontok életciklusát és műveleteit az Ön nevében – az ügynök-csomópontokhoz társított IaaS-erőforrások módosítása nem támogatott. Linux-csomópontok esetében azonban a démon-készletekkel telepítheti az olyan egyéni szoftvereket, mint a kártevők elleni megoldás.

- [Biztonsági riasztások hivatkozási útmutatója](../security-center/alerts-reference.md)

- [Tárolók riasztásai – Azure Kubernetes Service-fürtök](../security-center/alerts-reference.md#alerts-akscluster)

- [AK megosztott felelősség és démon-készletek](support-policies.md#shared-responsibility)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: az adatai biztonsági mentése megfelelő eszköz használatával a tárolási típushoz (például Velero), amely képes biztonsági másolatot készíteni az állandó kötetekről, valamint további fürterőforrás-és konfigurációkkal. Rendszeresen ellenőrizze a biztonsági másolatok integritását és biztonságát. 

A biztonsági mentés előtt távolítsa el az állapotokat az alkalmazásokból. Ha ez nem hajtható végre, biztonsági mentést készíthet az állandó kötetekről származó adatokról, és rendszeresen teszteli a visszaállítási műveleteket az adatok integritásának és a szükséges folyamatoknak az ellenőrzéséhez.

- [Ajánlott eljárások a tároláshoz és a biztonsági mentéshez az AK-ban](operator-best-practices-storage.md)

- [Ajánlott eljárások az üzletmenet folytonosságához és a vész-helyreállításhoz az AK-ban](operator-best-practices-multi-region.md)

- [A Azure Site Recovery megismerése](../site-recovery/site-recovery-overview.md)

- [Az Velero beállítása az Azure-ban](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: az adatai biztonsági mentése megfelelő eszköz használatával a tárolási típushoz (például Velero), amely képes biztonsági másolatot készíteni az állandó kötetekről, valamint további fürterőforrás-és konfigurációkkal. 

A PowerShell-parancsokkal a Key Vault tanúsítványok, kulcsok, felügyelt tárolási fiókok és titkok rendszeres automatikus biztonsági mentését végezheti el. 

Például:

Backup-AzKeyVaultCertificate Backup-AzKeyVaultKey Backup-AzKeyVaultManagedStorageAccount Backup-AzKeyVaultSecret

- [Key Vault tanúsítványok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Key Vault kulcsok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Key Vault felügyelt Storage-fiókok biztonsági mentése](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Key Vault titkos kódok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [A Azure Backup engedélyezése](../backup/index.yml)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Velero biztonsági másolaton belül rendszeresen hajtja végre a tartalom adat-visszaállítását. Ha szükséges, ellenőrizze a visszaállítást egy elkülönített virtuális hálózatra.

A PowerShell-parancsokkal rendszeres időközönként Key Vault tanúsítványok, kulcsok, felügyelt tárolási fiókok és titkok adat-visszaállítását végzi. 

Például:

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret

- [Key Vault tanúsítványok visszaállítása](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Key Vault kulcsok visszaállítása](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [Key Vault felügyelt Storage-fiókok visszaállítása](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault titkos kódok visszaállítása](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](../backup/backup-azure-restore-files-from-vm.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az adatai biztonsági mentése megfelelő eszköz használatával a tárolási típushoz (például Velero), amely képes biztonsági másolatot készíteni az állandó kötetekről, valamint további fürterőforrás-és konfigurációkkal. 

Az Azure Kubernetes Service (ak) környezetekben Azure Key Vault használata esetén engedélyezze a kulcsok véletlen vagy rosszindulatú törléssel szembeni védettségét Key Vault Soft-Delete.

- [Az Azure Storage Service Encryption ismertetése](../storage/common/storage-service-encryption.md)

- [A Soft-Delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](../security/benchmarks/security-control-incident-response.md)című témakörben talál.*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a riasztások fontossági sorrendbe állítása, amelyeknek a riasztásokat Security Center hozzárendelt súlyossággal kell megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.
Egyértelműen megjelölheti az előfizetéseket (például éles környezetben, nem éles környezetben), és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Tekintse át a NIST kiadványát, hogy azonosítsa a gyenge pontokat és a hiányosságokat, és szükség szerint módosítsa a tervet.

- [Útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: Security Center riasztások és javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Azt is megteheti, hogy a Security Center adatösszekötővel továbbítja a riasztásokat az Azure Sentinelnek a szervezeti üzleti követelmények alapján.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [További információt a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos, a Microsoft által felügyelt felhő-infrastruktúra,-szolgáltatások és-alkalmazások fejlesztésére vonatkozó stratégiáról és végrehajtásáról itt talál.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
