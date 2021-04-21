---
title: Az Azure biztonsági alapkonfigurációja Azure Kubernetes Service
description: A Azure Kubernetes Service alapkonfigurációja eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0564f1f39ac9d492dfffdf0e7adacdde08db0874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769588"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Az Azure biztonsági alapkonfigurációja Azure Kubernetes Service

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza az Azure Kubernetesre. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure  biztonsági teljesítménytesztje által meghatározott biztonsági vezérlők és az Azure Kubernetesre vonatkozó kapcsolódó útmutató szerint van csoportosítva. **Az** Azure Kubernetesre nem alkalmazható, illetve a Microsoft felelősségét felváló vezérlők ki vannak zárva.

Az Azure Kubernetes azure-biztonsági teljesítménytesztnek való teljes leképezését az Azure Kubernetes teljes biztonsági alapkonfiguráció-leképezési [fájljában () láthatja.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Alapértelmezés szerint a hálózati biztonsági csoport és az útvonaltábla automatikusan létrejön egy Microsoft Azure Kubernetes Service- (AKS-) fürt létrehozásával. Az AKS automatikusan módosítja a hálózati biztonsági csoportokat a megfelelő forgalomhoz, amikor a szolgáltatások terheléselelosztásokkal, portleképezésekkel vagy bejövő útvonalakkal vannak létrehozva. A hálózati biztonsági csoport automatikusan társítva van az ügyfélcsomópontokon lévő virtuális hálózati adapterekkel és az útvonaltáblával a virtuális hálózat alhálózatával. 

Az AKS hálózati házirendek használatával korlátozhatja a hálózati forgalmat a fürt Linux-podok közötti bejövő és bejövő forgalmára vonatkozó szabályok meghatározásával a választott névterek és címkeválasztók alapján. A hálózati szabályzat használatához Azure CNI virtuális hálózatokkal és alhálózatokkal rendelkező beépülő modul szükséges, és csak a fürt létrehozásakor engedélyezhető. Meglévő AKS-fürtön nem helyezhetők üzembe.

Privát AKS-fürtöt is megvalósíthat, hogy az AKS API-kiszolgáló és a csomópontkészletek közötti hálózati forgalom csak a magánhálózaton maradjon. A vezérlősík vagy AZ API-kiszolgáló egy AKS által felügyelt Azure-előfizetésben található, és belső (RFC1918) IP-címeket használ, míg az ügyfél fürtje vagy csomópontkészlete a saját előfizetésében található. A kiszolgáló és a fürt vagy a csomópontkészlet az API-kiszolgáló virtuális hálózatának Azure Private Link szolgáltatásával és az ügyfél AKS-fürtje alhálózatán elérhető privát végponttal kommunikál egymással.  Másik lehetőségként használjon nyilvános végpontot az AKS API-kiszolgálóhoz, de korlátozza a hozzáférést az AKS API-kiszolgáló Engedélyezett IP-tartományok funkciójának használatával. 

- [Az Azure Kubernetes Service (AKS) alkalmazásainak és fürtjeinek biztonsági fogalmai](concepts-security.md)

- [A podok közötti adatforgalom biztonságossá tere hálózati szabályzatokkal Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Privát fürt Azure Kubernetes Service létrehozása](private-clusters.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.ContainerService:**

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati hálózatok konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Használja Security Center és kövesse a hálózatvédelmi javaslatokat az AKS-fürtök által használt hálózati erőforrások Azure Kubernetes Service érdekében. 

Engedélyezze a hálózati biztonsági csoport forgalomnaplóit, és küldje el a naplókat egy Azure Storage-fióknak naplózásra. A forgalmi naplókat egy Log Analytics-munkaterületre is elküldheti, majd a Traffic Analytics használatával betekintést nyerhet az Azure-felhő forgalmi mintáiba, így vizualizálhatja a hálózati tevékenységeket, azonosíthatja a hasznos helyeket és a biztonsági fenyegetéseket, megértheti a forgalmi mintákat, és azonosíthatja a hálózati helytelen konfigurációkat.

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Hálózati biztonsági forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** Egy AKS-fürt Azure Application Gateway engedélyezett Web Application Firewall (WAF) használatával egy további biztonsági réteget nyújthat a webalkalmazások bejövő forgalmának szűrése által. Az Azure WAF a The Open Web Application Security Project (OWASP) által biztosított szabályokat használ az olyan támadásokhoz, mint például a webhelyközi parancsfájlok vagy a cookie-szenzálódás a forgalom ellen. 

Api-átjárót használhat az AKS-környezetben használt API-k hitelesítéséhez, engedélyezéséhez, szabályozásához, gyorsítótárazása, átalakításához és monitorozásához. Az API-átjárók a mikroszolgáltatások bejárati ajtaiként szolgálnak, leválasztják az ügyfeleket a mikroszolgáltatásokról, és csökkentik a mikroszolgáltatások összetettségét a keresztvágással kapcsolatos aggodalmak kezelésének terhének csökkentésével.

- [Az AKS hálózati kapcsolatának és biztonságának ajánlott eljárásai](operator-best-practices-network.md)

- [Application Gateway bejövő forgalom vezérlője ](../application-gateway/ingress-controller-overview.md)

- [Az Azure API Management a felhőben üzembe helyezett mikroszolgáltatásokkal Azure Kubernetes Service](../api-management/api-management-kubernetes.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** A Microsoft distributed Denial-of-service (DDoS) Standard szintű védelmének engedélyezése az olyan virtuális hálózatokon, amelyeken Azure Kubernetes Service (AKS) összetevők vannak telepítve a DDoS-támadások elleni védelem érdekében.

Telepítse a hálózati házirend motorját, és hozzon létre Kubernetes hálózati szabályzatokat az AKS-hez a podok közötti forgalom szabályozásához, mivel alapértelmezés szerint a podok közötti összes forgalom engedélyezett. A hálózati szabályzatot csak Linux-alapú csomópontokhoz és podokhoz szabad használni az AKS-ban. Olyan szabályokat határozhat meg, amelyek korlátozzák a podok kommunikációját a nagyobb biztonság érdekében. 

Engedélyezheti vagy megtagadhatja a forgalmat olyan beállítások alapján, mint a hozzárendelt címkék, a névtér vagy a forgalomport. A szükséges hálózati szabályzatok automatikusan alkalmazhatók, amikor a podok dinamikusan létrejönnek egy AKS-fürtben. 

- [A podok közötti adatforgalom biztonságossá tere hálózati szabályzatok használatával Azure Kubernetes Service (AKS)](use-network-policies.md)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** A Network Watcher tevékenységek kivizsgálásához szükség szerint használja a csomagrögzítést. 

Network Watcher automatikusan engedélyezve van a virtuális hálózat régiójában, amikor létrehoz vagy frissít egy virtuális hálózatot az előfizetésében. Új példányokat is létrehozhat a Network Watcher a PowerShell, az Azure CLI, a REST API vagy a Azure Resource Manager Client metódus használatával

- [A Network Watcher](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Biztonságossá Azure Kubernetes Service (AKS-) fürtöt egy Azure Application Gateway engedélyezett Web Application Firewall (WAF). 

Ha a behatolásészlelés és/vagy a viselkedéselemzésen alapuló behatolásészlelés és/vagy -megelőzés nem követelmény, a WAF-et adó Azure Application Gateway használható és konfigurálható "észlelési módban" a riasztások és fenyegetések naplózásához, vagy "megelőzési mód" segítségével az észlelt behatolások és támadások aktív blokkolásához.

- [Az AKS-fürt WAF-fel való biztonságossá tétele – ajánlott eljárások](https://docs.microsoft.com/azure/aks/operator-best-practices-network#secure-traffic-with-a-web-application-firewall-waf)

- [Üzembe helyezési Azure Application Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Virtuális hálózati szolgáltatáscímkékkel meghatározhatja a hálózati hozzáférés-vezérlést a Azure Kubernetes Service (AKS) példányokhoz társított hálózati biztonsági csoportokon. A szolgáltatáscímkék adott IP-címek helyett használhatók olyan biztonsági szabályok létrehozásakor, amelyek engedélyezik vagy megtagadják a megfelelő szolgáltatás forgalmát a szolgáltatáscímke nevének megadásával. 

A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkéket a címek változásával.

Azure-címke alkalmazása az AKS-fürt csomópontkészleteire. Ezek eltérnek a virtuális hálózati szolgáltatáscímkéktől, és a rendszer alkalmazza őket a csomópontkészlet minden csomópontjára, és a frissítésekkel megmarad. 

- [A szolgáltatáscímkék használata és használata](../virtual-network/service-tags-overview.md)

- [Az AKS-hez való NSG-k](support-policies.md)

- [A fürtcsomópontok (AKS) Azure Kubernetes Service forgalmának szabályozása](limit-egress-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Standard biztonsági konfigurációk definiálása és implementációja Azure Policy AKS-fürtökhöz társított Azure Kubernetes Service hálózati erőforrásokhoz. 

A Azure Policy a "Microsoft.ContainerService" és a "Microsoft.Network" névterek aliasai használatával egyéni szabályzatokat hozhat létre az AKS-fürtök hálózati konfigurációjának naplózásához vagy kényszerítéhez. 

Az AKS-hez kapcsolódó beépített szabályzatdefiníciókat is használjon, például:

- Az engedélyezett IP-címtartományokat a Kubernetes Servicesben kell meghatározni

- HTTPS bejövő forgalom kényszerítése Kubernetes-fürtben

- Győződjön meg arról, hogy a szolgáltatások csak az engedélyezett portokon figyelnek a Kubernetes-fürtben

További információk a hivatkozott hivatkozásokon érhetők el.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták hálózattal való építéshez](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata hálózati biztonsági csoportokhoz és más erőforrásokhoz az AKS Azure Kubernetes Service (AKS-) fürtök be- és Azure Kubernetes Service forgalomhoz. Az egyes hálózati biztonsági csoportok szabályaihoz a "Leírás" mezőben adhatja meg az üzleti igényeket és/vagy időtartamot stb. a hálózatra/hálózatról be- és vissza forgalmat engedélyező szabályokra.

Használja a beépített Azure Policy címkézéssel kapcsolatos definíciókat, például a "Címke és az érték megkövetelése" definíciókat, amelyek biztosítják, hogy minden erőforrás címkékkel legyen létrehozva, és értesítéseket fogad a meglévő, címkézetlen erőforrásokról.

A fürtön belüli adott hálózati útvonalak engedélyezése vagy megtagadása névterek és hálózati házirendekkel jelölt címkeválasztók alapján. Ezeket a névtereket és címkéket használhatja leíróként a forgalom konfigurációs szabályaihoz. A Azure PowerShell azure parancssori felület (CLI) használatával erőforrásokat keres vagy hajt végre a címkék alapján.

- [Azure Policy cli-val](/cli/azure/policy)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével monitorozható a hálózati erőforrások konfigurációja, és észlelhetők a hálózati erőforrások Azure Kubernetes Service (AKS) fürtökhöz kapcsolódó változások. 

Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak. Az AzureContainerService felhasználó tevékenységnaplókban szereplő bejegyzései platformműveletekként vannak naplózva. 

A Azure Monitor naplók használatával engedélyezheti és lekérdezheti a naplókat az AKS-ről a fő összetevők, a kube-apiserver és a kube-controller-manager használatával. Hozza létre és kezelje a kubeletet tároló-futásidejű csomópontokkal, és telepítse az alkalmazásokat a felügyelt Kubernetes API-kiszolgálón keresztül. 

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [A Kubernetes főcsomópont-naplóinak engedélyezése és áttekintése az Azure Kubernetes Service-ben (AKS)](/azure/aks/view-master-logs)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizálási források használata

**Útmutató:** Azure Kubernetes Service (AKS)-csomópontok ntp.ubuntu.com használnak az időszinkronizáláshoz, valamint a 123-as UDP-portot és a Network Time Protocolt (NTP). 

Ha egyéni DNS-kiszolgálókat használ, győződjön meg arról, hogy az NTP-kiszolgálók elérhetők a fürtcsomópontok számára. 

- [Az AKS-fürtcsomópontok NTP-tartomány- és portkövetelményei](limit-egress-traffic.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Auditnaplók engedélyezése az Azure Kubernetes Services (AKS) felügyelt szolgáltatásként biztosított fő összetevőiből, a kube-apiserverből és a kube-controller-managerből. 

- kube-auditaksService: A vezérlősík-művelet megjelenített neve az auditnaplóban (a hcpService szolgáltatásból) 

- masterclient: A MasterClientCertificate auditnaplójában megjelenő név, az az aks get-credentials által lekért tanúsítvány 

- nodeclient: A ClientCertificate megjelenített neve, amelyet az ügynökcsomópontok használnak

Engedélyezzen más auditnaplókat is, például a kube-auditot. 

Exportálja ezeket a naplókat a Log Analyticsbe vagy egy másik tárolási platformra. A Azure Monitor Log Analytics-munkaterületek használatával végezhet lekérdezést és elemzést, az Azure Storage-fiókokat pedig hosszú távú és archiválási tárterületre használhatja.

Az adatok engedélyezésével és beállításával Azure Sentinel külső SIEM-et a szervezeti üzleti követelmények alapján.

- [Tekintse át a naplósémát és a naplószerepk szerepköröket itt](/azure/aks/view-master-logs)

- [A Azure Monitor tárolókhoz való Azure Monitor](/azure/azure-monitor/insights/container-insights-overview)

- [A tárolók Azure Monitor engedélyezése](/azure/azure-monitor/insights/container-insights-onboard)

- [A Kubernetes főcsomópont-naplóinak engedélyezése és áttekintése az Azure Kubernetes Service-ben (AKS)](/azure/aks/view-master-logs)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** A tevékenységnaplók segítségével figyelheti a Azure Kubernetes Service (AKS) erőforrásain végzett műveleteket az összes tevékenység és azok állapotának megtekintéséhez. Állapítsa meg, milyen műveleteket végzett az előfizetés erőforrásain a tevékenységnaplók segítségével: 

- ki indította el a műveletet
- a művelet beestekor
- a művelet állapota
- egyéb tulajdonságok értékei, amelyek segíthetnek a művelet kutatásában

Információk lekérése a tevékenységnaplóból a Azure PowerShell, az Azure parancssori felület (CLI), az Azure REST API vagy a Azure Portal. 

Engedélyezze az auditnaplókat az AKS fő összetevőin, például: 

- kube-auditaksService: A vezérlősík-művelet megjelenített neve az auditnaplóban (a hcpService szolgáltatásból) 

- masterclient: A MasterClientCertificate auditnaplójában megjelenő név, az az aks get-credentials által lekért tanúsítvány 

- nodeclient: A ClientCertificate megjelenített neve, amelyet az ügynökcsomópontok használnak

Kapcsolja be az egyéb auditnaplókat is, például a kube-auditot. 

- [Kubernetes-főcsomópontnaplók engedélyezése és áttekintése az AKS-ban](/azure/aks/view-master-logs)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése operációs rendszerekről

**Útmutató:** Log Analytics-ügynökök automatikus telepítésének engedélyezése az AKS-fürtcsomópontokról való adatgyűjtéshez. Emellett kapcsolja be az Azure Log Analytics Monitoring Agent automatikus üzembe Azure Security Center, mivel az automatikus kiépítés alapértelmezés szerint ki van kapcsolva. Az ügynök manuálisan is telepíthető. Az automatikus üzembe helyezés után a Security Center a Log Analytics-ügynököt az összes támogatott Azure-beli és újonnan létrehozott virtuális gépre telepíti. A Security Center adatokat gyűjt az Azure Virtual Machines-ről, virtuálisgép-méretezési csoportokról és IaaS-tárolókról, például a Kubernetes-fürtcsomópontokról a biztonsági rések és fenyegetések figyelése érdekében. Az adatokat az Azure Log Analytics-ügynökkel gyűjti a rendszer, amely beolvassa a biztonsággal kapcsolatos különböző konfigurációkat és eseménynaplókat a gépről, és elemzés céljából átmásolja az adatokat a munkaterületre. 

Az adatgyűjtés a hiányzó frissítések, az operációs rendszer helytelenül konfigurált biztonsági beállításai, a végpontvédelmi állapot, valamint az állapot- és fenyegetésészlelés láthatóságának érdekében szükséges.

- [A Log Analytics-ügynök automatikus kiépítésének engedélyezése](../security-center/security-center-enable-data-collection.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: Biztonsági naplók tárolásának konfigurálása

**Útmutató:** A Azure Kubernetes Service (AKS) példányainak Azure Monitor, és a szervezet megfelelőségi követelményeinek megfelelően állítsa be az Azure Log Analytics-munkaterület megfelelő megőrzési időszakát. 

- [Naplómegőrzési paraméterek beállítása Log Analytics-munkaterületeken](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Az Azure Kubernetes Service (AKS) példányainak Azure Monitor konfigurálhatja a fürt diagnosztikai beállításait. 

A Azure Monitor Log Analytics-munkaterületén áttekinthet naplókat, és lekérdezéseket hajthatja végre a naplóadatokon. Azure Monitor-naplók az Azure Portal-ban vagy a CLI-n keresztül engedélyezhetők és kezelhetők, és kubernetes szerepköralapú hozzáférés-vezérléssel (Kubernetes RBAC), Azure RBAC-val és nem RBAC-kompatibilis AKS-fürtökhöz is használhatók.

Tekintse meg az AKS-főösszetevők (kube-apiserver és kube-controllermanager) által létrehozott naplókat az alkalmazás és a szolgáltatások hibaelhárításához. Az adatok központi naplókezeléshez és -Azure Sentinel külső SIEM-hez történő központi naplózáshoz és adatbevezetéshez.

- [Kubernetes főcsomópont-naplók engedélyezése és áttekintése az AKS-ban](/azure/aks/view-master-logs)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Az Azure Kubernetes Service (AKS) és a Security Center segítségével mélyebb betekintést nyerhet az AKS-csomópontokbe. 

Tekintse Security Center gazdagépen és fürtszinten észlelt fenyegetésekre és rosszindulatú tevékenységekre vonatkozó riasztásokat. Security Center az AKS-fürtökben előforduló nyers biztonsági események folyamatos elemzését valósítja meg, például a hálózati adatokat, a folyamat létrehozását és a Kubernetes-naplót. Állapítsa meg, hogy ez a tevékenység várt viselkedés-e, vagy hogy az alkalmazás nem megfelelően működik-e. A metrikák és naplók Azure Monitor az eredményeket. 

- [Az Azure Kubernetes Services és a Security Center](../security-center/defender-for-kubernetes-introduction.md)

- [A standard Azure Security Center engedélyezése](../security-center/security-center-get-started.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosította

**Útmutató:** A Microsoft Anti-malware for Azure telepítése és engedélyezése Azure Kubernetes Service (AKS) virtuális gépek és virtuálisgép-méretezési csoport csomópontjainak telepítéséhez. Tekintse át a Security Center szervizelésre vonatkozó riasztásokat.

- [Microsoft Antimalware és Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

- [Biztonsági riasztások referencia-útmutatója](../security-center/alerts-reference.md)

- [Tárolókra vonatkozó riasztások – Azure Kubernetes Service fürtök](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-lekérdezésnaplózás engedélyezése

**Útmutató:** Azure Kubernetes Service (AKS) a CoreDNS projektet használja a fürt DNS-kezeléséhez és megoldáshoz.

Engedélyezze a DNS-lekérdezések naplózását a coredns-custom ConfigMap fájlban dokumentált konfiguráció alkalmazásával. 

- [A CoreDNS testreszabása Azure Kubernetes Service-szel](coredns-custom.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutató:** A kubectl parancssori ügyfél használatával az Azure Kubernetes Service-ban (AKS) kezelheti a Kubernetes-fürtöket, és le tudja szerezni a naplóit az AKS-csomópontról hibaelhárítási célokra. A Kubectl már telepítve van, ha a Azure Cloud Shell. A kubectl helyi telepítéséhez használja az Install-AzAksKubectl parancsmagot.

- [Rövid útmutató – Fürt üzembe helyezése Azure Kubernetes Service PowerShell használatával](kubernetes-walkthrough-powershell.md)

- [Kubelet-naplók lekérése Azure Kubernetes Service- (AKS-) fürtcsomópontokból](kubelet-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Azure Kubernetes Service (AKS) maga nem biztosít olyan identitáskezelési megoldást, amely normál felhasználói fiókokat és jelszavakat tárol. A Azure Active Directory (Azure AD) integrációjával hozzáférést adhat a felhasználóknak vagy csoportoknak a Kubernetes-erőforrásokhoz egy névtérben vagy a fürtön belül.

Alkalmi lekérdezések végrehajtása az AKS felügyeleti csoportok tagjainak felderítéséhez az Azure AD PowerShell-modullal

Az Azure CLI-t olyan műveletekhez használhatja, mint a "Get access credentials for a managed Kubernetes cluster" (Felügyelt Kubernetes-fürt hozzáférési hitelesítő adatainak le szolgáltatása) a hozzáférés rendszeres egyeztetéséhez. Ezt a folyamatot implementálja a szolgáltatásfiókok frissített leltárának megtartásához, amelyek az AKS egy másik elsődleges felhasználótípusai. Kikényszeríteni Security Center identitás- és hozzáférés-kezelési javaslatait.

- [Az AKS integrálása az Azure AD-val](azure-ad-integration-cli.md)

- [Címtárbeli szerepkör tagjainak lekért használata az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** Azure Kubernetes Service (AKS) nem rendelkezik az általános alapértelmezett jelszavak fogalmával, és nem biztosít olyan identitáskezelési megoldást, amelyben a normál felhasználói fiókok és jelszavak tárolhatók. A Azure Active Directory (Azure AD) integrációjával szerepköralapú hozzáférést adhat az AKS-erőforrásokhoz egy névtérben vagy a fürtön belül. 

Alkalmi lekérdezések végrehajtása az AKS felügyeleti csoportok tagjainak felderítéséhez az Azure AD PowerShell-modullal

- [Az AKS hozzáférési és identitási beállításainak](concepts-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Integrálja a felhasználói hitelesítést Azure Kubernetes Service (AKS-) fürtökhöz az Azure Active Directory (Azure AD) használatával. Jelentkezzen be egy AKS-fürtbe egy Azure AD-hitelesítési jogkivonattal. Konfigurálja a Kubernetes szerepköralapú hozzáférés-vezérlését (Kubernetes RBAC) a Kubernetes-konfiguráció (Kubeconfig) információihoz és engedélyéhez, névteréhez és fürterőforrásaihoz való rendszergazdai hozzáféréshez. 

Hozzon létre szabályzatokat és eljárásokat a dedikált rendszergazdai fiókok használatával kapcsolatban. Implementálja Security Center identitás- és hozzáférés-kezelési javaslatokat.

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

- [Fürterőforrásokhoz való hozzáférés szabályozása](azure-ad-rbac.md)

- [Az Azure szerepköralapú hozzáférés-vezérlésének használata](control-kubeconfig-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató:** Egyszeri bejelentkezés használata Azure Kubernetes Service (AKS) és Azure Active Directory (Azure AD) integrált hitelesítéssel egy AKS-fürthöz.

- [Kubernetes-naplók, -események és -podmetrikák megtekintése valós időben](/azure/azure-monitor/insights/container-insights-livedata-overview)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory hozzáféréshez

**Útmutató:** Az Azure Kubernetes Service (AKS) hitelesítésének integrálása Azure Active Directory (Azure AD) használatával. 

Engedélyezze az Azure AD többtényezős hitelesítést, és kövesse Security Center identitás- és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomás (PAW) használata többtényezős hitelesítéssel (MFA), amely a megadott Azure Kubernetes Service- (AKS-) fürtökbe és a kapcsolódó erőforrásokba való bejelentkezésre van konfigurálva.

- [Tudnivalók az emelt szintű hozzáférésű munkaállomásról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés (MFA) engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Azure Active Directory (Azure AD) biztonsági jelentések használata Azure AD-integrált hitelesítéssel az Azure Kubernetes Service (AKS) szolgáltatáshoz. Riasztások akkor jönnek létre, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. A Security Center identitás- és hozzáférési tevékenységek figyelése.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitás- és hozzáférési tevékenységének figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés elnevezett helyeinek használatával Azure Kubernetes Service (AKS)-fürtök csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításaiból való hozzáférését engedélyezheti. Ehhez integrált hitelesítés szükséges az AKS-hez Azure Active Directory (Azure AD) használatával.

Korlátozza az AKS API-kiszolgálóhoz való hozzáférést az IP-címtartományok korlátozott készlete számára, mivel a fürtön műveletek elvégzésére vonatkozó kéréseket kap az erőforrások létrehozásához vagy a csomópontok számának méretezéséhez. 

- [Biztonságos hozzáférés az API-kiszolgálóhoz hitelesített IP-címtartományok használatával a Azure Kubernetes Service (AKS)](api-server-authorized-ip-ranges.md)

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként a Azure Kubernetes Service (AKS) számára. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt, valamint a sót, a hasheket, valamint biztonságosan tárolja a felhasználói hitelesítő adatokat.

Az AKS beépített szerepkörei használata azure-beli szerepköralapú hozzáférés-vezérléssel (Azure RBAC) – Erőforrás-szabályzatok közreműködője és tulajdonosa a Kubernetes-fürthöz való szabályzat-hozzárendelési műveletekhez

- [Azure Policy áttekintése](../governance/policy/overview.md)

- [Az Azure AD integrálása az AKS-sel](azure-ad-integration-cli.md)

- [Az AKS által felügyelt Azure AD integrálása](managed-aad.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) biztonsági jelentések használata Azure AD-integrált hitelesítéssel az Azure Kubernetes Service (AKS) szolgáltatáshoz. Az elavult fiókok felderítése az Azure AD-naplókban. 

Azure Identity Access-felülvizsgálatok végrehajtása a csoporttagság, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések hatékony kezeléséhez. Az identitással és hozzáféréssel kapcsolatos javaslatok szervizlének Security Center.

Vegye figyelembe a támogatási vagy hibaelhárítási célokra használt szerepköröket. Például a Microsoft ügyfélszolgálata által (felhasználói jóváhagyással) a fürtműveleteket az aks-support-rolebinding nevű beépített Kubernetes "edit" szerepkörével adhatja meg. Ezzel a szerepkörsel az AKS-támogatás engedélyezve van a fürtkonfiguráció és az erőforrások szerkesztéséhez a fürtproblémák elhárítása és diagnosztizálása érdekében. Ez a szerepkör azonban nem módosíthatja az engedélyeket, és nem hozhat létre szerepköröket vagy szerepkörkötéseket. Ez a szerepkör-hozzáférés csak az aktív támogatási jegyeken van engedélyezve igény szerinti (JIT) hozzáféréssel.
 
- [Hozzáférési és identitás-beállítások az Azure Kubernetes Service (AKS) szolgáltatáshoz](concepts-identity.md)

- [Az Azure Identity Access-felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [A felhasználók identitási és hozzáférési tevékenységének figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Felhasználói hitelesítés integrálása Azure Kubernetes Service (AKS) és Azure Active Directory (Azure AD) használatával. Diagnosztikai beállítások létrehozása az Azure AD-hez, az audit- és bejelentkezési naplók elküldése egy Azure Log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat (például amikor egy inaktivált fiók megpróbál bejelentkezni) egy Azure Log Analytics-munkaterületen.
- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Naplóriasztás létrehozása, megtekintése és kezelése Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Felhasználói hitelesítés integrálása Azure Kubernetes Service (AKS) és Azure Active Directory (Azure AD) szolgáltatásba. Az Azure AD Kockázatészlelési és Identity Protection szolgáltatásával konfigurálhatja az automatikus válaszokat a felhasználói identitásokkal kapcsolatos gyanús műveletekre. Az adatokat az Azure Sentinel üzleti igényeken alapuló további vizsgálatokhoz adatokat kell behozni.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** Címkék használata a Azure Kubernetes Service (AKS) üzemelő példányaihoz kapcsolódó erőforrásokon a bizalmas adatokat tároló vagy feldolgozást tároló Azure-erőforrások nyomon követésének segítése érdekében.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Felügyelt fürtök címkéinek frissítése](/rest/api/aks/managedclusters/updatetags)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Azonos fürtön belül a csapatok és a számítási feladatok logikai elkülönítése Azure Kubernetes Service (AKS) segítségével, hogy a legkevesebb jogosultságot biztosítsa, és az egyes csapatok által megkövetelt erőforrásokra terjed ki. 

Logikai elkülönítési határ létrehozásához használja a Kubernetesben a névteret. Fontolja meg további Kubernetes-funkciók használatát az elkülönítés és a több-bérlős rendszer, például az ütemezés, a hálózat, a hitelesítés/engedélyezés és a tárolók érdekében.

Külön előfizetéseket és/vagy felügyeleti csoportokat implementálja a fejlesztési, tesztelési és éles környezetekhez. Külön AKS-fürtök hálózattal. Ezeket külön virtuális hálózatokon kell üzembe helyezni, amelyek megfelelően vannak megcímkézve.

- [A fürtelszigetelés ajánlott eljárásai az AKS-ban](operator-best-practices-cluster-isolation.md)

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Az AKS hálózati kapcsolatának és biztonságának ajánlott eljárásai](operator-best-practices-network.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** Külső megoldás használata a Azure Marketplace peremhálózatán, amely figyeli a bizalmas információk jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztja az információbiztonsági szakembereket.

A Microsoft kezeli a mögöttes platformot, és minden ügyféltartalmat bizalmasnak kezel, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az AKS-funkciókhoz szükséges portok, címek és tartománynevek listája](limit-egress-traffic.md)

- [Diagnosztikai beállítások konfigurálása a Azure Firewall](../firewall/firewall-diagnostics.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** HTTPS bejövő forgalomvezérlő létrehozása és saját TLS-tanúsítványok használata (vagy opcionálisan Let's Encrypt) a Azure Kubernetes Service (AKS) üzemelő példányai számára. 

A Kubernetes bejövő forgalma alapértelmezés szerint HTTPS/TLS protokollon keresztül van titkosítva. A további monitorozás érdekében tekintse át az AKS-példányok esetlegesen titkosítatlan bejövő forgalmát. Ez egyes esetekben magában foglalhatja az NTP-forgalmat, a DNS-forgalmat és a HTTP-forgalmat a frissítések leolvasása érdekében. 

- [HTTPS bejövő forgalomvezérlő létrehozása az AKS-hez és saját TLS-tanúsítványok használata](ingress-own-tls.md)

- [HTTPS bejövő forgalomvezérlő létrehozása az AKS-hez a Let's Encrypt használatával](ingress-tls.md)

- [Az AKS által használt potenciálisan kieső portok és protokollok listája](limit-egress-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: A bizalmas adatok azonosítása aktív felderítési eszközzel

**Útmutató:** Az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el az Azure Storage- vagy számítási erőforrásokhoz. Ha megfelelőségi célokból szükség van rá, implementálja a harmadik féltől származó megoldást.
A Microsoft kezeli a mögöttes platformot, és minden ügyféltartalmat bizalmasként kezel, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. 

Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Az Erőforrásokhoz való hozzáférés kezelése az Azure RBAC használatával

**Útmutató:** Az Azure-erőforrások részletes hozzáférés-kezeléséhez használja a Azure Resource Manager-re épülő Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) engedélyezési rendszert.

A Azure Kubernetes Service (AKS) konfigurálása a Azure Active Directory (Azure AD) felhasználói hitelesítéshez való használatára. Jelentkezzen be egy AKS-fürtbe az Azure AD hitelesítési jogkivonatával ezzel a konfigurációval. 

A beépített AKS-szerepkörök használata az Azure RBAC erőforrás-szabályzatok közreműködőjéhez és tulajdonosához az AKS-fürthöz való szabályzat-hozzárendelési műveletekhez

- [Fürterőforrásokhoz való hozzáférés szabályozása az Azure RBAC és az Azure AD Identities használatával az AKS-ban](azure-ad-rbac.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.ContainerService:**

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató:** Az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el az Azure Storage- vagy számítási erőforrásokhoz. Ha megfelelőségi célokból szükség van rá, implementálja a harmadik féltől származó megoldást.
A Microsoft kezeli a mögöttes platformot, és minden ügyféltartalmat bizalmasnak kezel, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** A Azure Kubernetes Service -kötetek (AKS) kötetei számára biztosított két elsődleges tárolótípust Azure-lemezek vagy Azure Files. Mindkét tárolótípus az Azure Storage Service Encryption (SSE) használatával titkosítja az adatokat a biztonság növelése érdekében. Az adatok alapértelmezés szerint a Microsoft által kezelt kulcsokkal vannak titkosítva.

Az ügyfelek által felügyelt kulcsokkal való, az AKS-fürtökön lévő operációsrendszer- és adatlemezek titkosításához is elérhető a titkosítási kulcsok további szabályozása érdekében. Az ügyfelek felelősek a kulcskezelési tevékenységekért, például a kulcs biztonsági mentésért és a rotációért. A lemezek jelenleg nem titkosíthatóak Azure Disk Encryption AKS-csomópontszinten lévő virtuális merevlemezekkel.

- [Ajánlott eljárások az adattároláshoz és a biztonsági mentéshez Azure Kubernetes Service (AKS)](operator-best-practices-storage.md)

- [Saját kulcsok használata (BYOK) Azure-lemezekkel a Azure Kubernetes Service (AKS)](azure-disk-customer-managed-keys.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Monitor tárolókhoz való használatával figyelheti a felügyelt Kubernetes-fürtökben üzembe helyezett tárolók számítási feladatainak teljesítményét, amelyek a Azure Kubernetes Service (AKS) vannak üzemeltetve. 

Riasztásokat konfigurálhat proaktív értesítésekhez vagy naplók létrehozásához, ha a processzor és a memória kihasználtsága meghaladja a megadott küszöbértékeket, vagy ha állapotváltozás történik a fürtben az infrastruktúra vagy a csomópontok állapotának összesítésekor. 

Az Azure-tevékenységnaplóval magas szinten figyelheti az AKS-fürtöt és a kapcsolódó erőforrásokat. Integrálhatja a Prometheus-t a csomópontok és a Kubernetes által gyűjtött alkalmazás- és számítási feladatok metrika megtekintéséhez lekérdezések használatával egyéni riasztások, irányítópultok létrehozásához és részletes elemzések elvégzéséhez.

- [A Azure Monitor tárolókhoz való Azure Monitor](/azure/azure-monitor/insights/container-insights-overview)

- [Tárolók Azure Monitor engedélyezése](/azure/azure-monitor/insights/container-insights-onboard)

- [Azure-tevékenységnapló eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált sebezhetőség-ellenőrzési eszközök futtatása

**Útmutató:** A Security Center a biztonsági rések Azure Container Registry beleértve Azure Kubernetes Service (AKS) példányait is. Engedélyezze a container regisztries csomagot a Security Center, hogy Security Center készen állni a regisztrációs adatbázisba leküldt rendszerképek vizsgálatához.

Értesítést kap a Security Center irányítópultján, ha problémákat talál, Security Center a Qualys használatával vizsgálja a képet. A Container Registries csomag funkcióval mélyebb betekintést nyújt a tárolóregisztrálókban használt Azure Resource Manager biztonsági réseibe. 

A Security Center az összes biztonsági résre vonatkozó, beavatkozást lehetővé t tő javaslatokhoz. Ezek a javaslatok súlyossági besorolást és útmutatást tartalmaznak a szervizeléshez. 

- [Ajánlott eljárások a tároló rendszerképének kezeléséhez és biztonságának Azure Kubernetes Service (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [A tároló rendszerképének kezeléséhez és biztonságának ajánlott eljárásai az AKS-hez](operator-best-practices-container-image-management.md)

- [A Container Registry és a Azure Security Center](../security-center/defender-for-container-registries-introduction.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának üzembe helyezése

**Útmutató:** A rendszer automatikusan alkalmazza a biztonsági frissítéseket a Linux-csomópontokra az ügyfél Azure Kubernetes Service (AKS)-fürtök védelme érdekében. Ezek a frissítések közé tartoznak az operációs rendszer biztonsági javításai vagy a kernelfrissítések. 

Vegye figyelembe, hogy a Windows Server-csomópontok naprakészen tartási folyamata eltér a Linuxot futtató csomópontok folyamattól, mivel a Windows Server-csomópontok nem kapnak napi frissítéseket. Ehelyett az ügyfeleknek frissítést kell végezniük az AKS-fürtjeikben található Windows Server-csomópontkészleten, amely új csomópontokat helyez üzembe a legújabb alap Windows Server-rendszerképekkel és -javításokkal az Azure vezérlőpultján vagy az Azure CLI-val. Ezek a frissítések az AKS biztonsági vagy funkcióival kapcsolatos fejlesztéseket tartalmaznak.

- [A frissítések Linuxot futtató AKS-fürtcsomópontokra való alkalmazása](node-updates-kured.md)

- [AKS-csomópontkészlet frissítése Windows Server-csomópontokat használÓ AKS-fürtökhöz](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#upgrade-a-node-pool)

- [Azure Kubernetes Service (AKS) csomópont rendszerképének frissítései](node-image-upgrade.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Automatikus javításkezelési megoldás üzembe helyezése külső szoftvercímek számára

**Útmutató:** Manuális folyamat megvalósítása annak biztosításához, hogy Azure Kubernetes Service (AKS) fürtcsomópont harmadik féltől származó alkalmazásai a fürt élettartamának teljes időtartamára javítás alatt maradjanak. Ehhez szükség lehet az automatikus frissítések engedélyezésére, a csomópontok figyelésére vagy rendszeres újraindításokra.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.ContainerService:**

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: A biztonsági rések biztonsági rések biztonsági vizsgálatának összehasonlítása

**Útmutató:** A Security Center időközönként exportálja a vizsgálati eredményeket, és hasonlítsa össze az eredményeket annak ellenőrzéséhez, hogy a biztonsági rések ki lett-e orvosolva.

A "Get-AzSecurityTask" PowerShell-parancsmaggal automatizálhatja az Security Center által javasolt biztonsági feladatok lekérését a biztonsági helyzet és a szervizelés biztonsági rések vizsgálatának megerősítése érdekében.

- [A PowerShell használata a biztonsági rések megtekintésére a Azure Security Center](/powershell/module/az.security/get-azsecuritytask)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** A biztonsági rések rangsorolásának Security Center a biztonsági rések rangsorolása a biztonsági rések által biztosított súlyossági minősítéssel. 

A Common Vulnerability Scoring System (CVSS) (vagy a vizsgálati eszköz által biztosított más pontozási rendszerek) használata, ha beépített sebezhetőségi felmérési eszközt (például Qualys vagy Rapid7) használ, amelyet az Azure kínál.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph lekérdezheti/felderítheti az előfizetések összes erőforrását (például számítási, tárolási, hálózati stb.). Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes enumerálni az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

Bár a klasszikus Azure-erőforrások a Resource Graph is felderíthetőek, erősen ajánlott a Azure Resource Manager-alapú erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása azure-erőforrásokra metaadatokkal, hogy logikailag rendszerezze őket egy taxonómiába.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az eszközök rendszerezéséhez és nyomon követéséhez címkézést, felügyeleti csoportokat és különálló előfizetéseket használhat, ha szükséges. 

Fertőzöttek, címkék vagy címkék alkalmazása a Azure Kubernetes Service (AKS) csomópontkészletek létrehozásakor. Az adott csomópontkészletben lévő összes csomópont örökli ezt a fertőzött, címkés vagy címkét is.

A fertőzöttség, a címkék vagy a címkék segítségével rendszeresen egyeztetheti a leltárat, és biztosíthatja, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Felhasználói címkék létrehozása](../azure-resource-manager/management/tag-resources.md)

- [Felügyelt fürtök – Címkék frissítése](/rest/api/aks/managedclusters/updatetags)

- [Fertőzöttság, címke vagy címke megadása egy csomópontkészlethez](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és nyilvántartása

**Útmutató:** A jóváhagyott Azure-erőforrások és a számítási erőforrások jóváhagyott szoftverének meghatározása a vállalati üzleti igények alapján.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:
-   Nem engedélyezett erőforrástípusok 

-   Engedélyezett erőforrástípusok

A Azure Resource Graph használatával lekérdezheti/felderítheti az előfizetések erőforrásait. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva a szervezeti üzleti követelmények alapján.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató:** A Azure Automation változáskövetés és leltározás a környezetben telepített szoftverek megkereséhez. 

Gyűjtse össze és tekintse meg a számítógépeken található szoftverek, fájlok, Linux-démonok, Windows-szolgáltatások és Windows-beállításkulcsok leltárát, és figyelje a nem jóváhagyott szoftveralkalmazásokat. 

Kövesse nyomon a gépek konfigurációját, hogy segítve a környezet működési problémáinak pontos követését és a gépek állapotának jobb átértét.

- [Az Azure-beli virtuális gépek leltárának engedélyezése](../automation/automation-tutorial-installed-software.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** A Azure Automation változáskövetés és leltározás a környezetben telepített szoftverek megkereséhez. 

Gyűjtse össze és tekintse meg a számítógépeken található szoftverek, fájlok, Linux-démonok, Windows-szolgáltatások és Windows-beállításkulcsok leltárát, és figyelje a nem jóváhagyott szoftveralkalmazásokat. 

Kövesse nyomon a gépek konfigurációját, hogy segítve a környezet működési problémáinak pontos követését és a gépek állapotának jobb átértét.

- [Az Azure-beli virtuális gépek leltárának engedélyezése](../automation/automation-tutorial-installed-software.md)

- [A fájlintegritás monitorozásának használata](../security-center/security-center-file-integrity-monitoring.md)

- [Az Azure Change Tracking](../automation/change-tracking/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazások használata

**Útmutató:** A Azure Automation változáskövetés és leltározás a környezetben telepített szoftverek megkereséhez. 

Gyűjtse össze és tekintse meg a számítógépeken található szoftverek, fájlok, Linux-démonok, Windows-szolgáltatások és Windows-beállításkulcsok leltárát, és figyelje a nem jóváhagyott szoftveralkalmazásokat. 

Kövesse nyomon a gépek konfigurációját, hogy segítve a környezet működési problémáinak pontos követését és a gépek állapotának jobb átértét.

Engedélyezze az adaptív alkalmazáselemzést a Security Center a környezetben található alkalmazásokhoz.

- [Az Azure-beli virtuális gépek leltárának engedélyezése](../automation/automation-tutorial-installed-software.md)

- [Az adaptív Azure Security Center használata](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

A Azure Resource Graph lekérdezheti/felderítheti az előfizetések erőforrásait. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: A jóváhagyott szoftvercímek leltárának fenntartása

**Útmutató:** Azure Policy a beépített szabályzatdefiníciók használatával az előfizetésében létre lehet hozható erőforrások típusára vonatkozó korlátozásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure-beli feltételes hozzáféréssel korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "Hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.
- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: A felhasználók szkriptek számítási erőforrásokban való végrehajtásának korlátozása

**Útmutató:** Azure Kubernetes Service (AKS) önmagában nem biztosít identitáskezelési megoldást a normál felhasználói fiókok és jelszavak tárolására. Ehelyett használja a Azure Active Directory (Azure AD) megoldást az AKS-fürtök integrált identitásmegoldásaként.

Azure AD-integráció használatával hozzáférést adhat a felhasználóknak vagy csoportoknak a Kubernetes-erőforrásokhoz egy névtérben vagy a fürtön belül.

Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajthatja végre az AKS-rendszergazdai csoportok tagjainak felderítéséhez, és a hozzáférés rendszeres egyeztetéséhez használhatja. Az Azure CLI-t olyan műveletekhez használhatja, mint a "Get access credentials for a managed Kubernetes cluster" (Hozzáférési hitelesítő adatok le szolgáltatása felügyelt Kubernetes-fürthöz). Implementálja Security Center identitás- és hozzáférés-kezelési javaslatokat.

- [Az AKS kezelése az Azure CLI-val](/cli/azure/aks)

- [Az AKS- és az Azure AD-integráció](concepts-identity.md)

- [Az AKS integrálása az Azure AD-val](azure-ad-integration-cli.md)

- [Címtárszerepkomó lekért szerepkör az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: A magas kockázatú alkalmazások fizikai vagy logikai elkülönítése

**Útmutató:** Az Azure Kubernetes Service (AKS) funkcióival logikailag elkülönítheti az egy fürtben található csapatokat és számítási feladatokat a legkevesebb jogosultság érdekében, az egyes csapatok által megkövetelt erőforrásokra terjed ki. 

Implementálja a névteret a Kubernetesben egy logikai elkülönítési határ létrehozásához. A Azure Policy (AKS)-példányok konfigurációjának naplózására vagy kényszerítére egyéni szabályzatokat hozhat létre a "Microsoft.ContainerService" névtérben található Azure Kubernetes Service aliasokkal. 

Tekintse át és implementálja a Kubernetes további funkcióit, valamint az elkülönítéssel és a több-bérlős rendszerekkel kapcsolatos szempontokat, például az ütemezést, a hálózatépítést, a hitelesítést/engedélyezést és a tárolókat. Emellett használjon külön előfizetéseket és felügyeleti csoportokat a fejlesztéshez, a teszteléshez és az éles környezethez. Külön AKS-fürtök virtuális hálózatokkal és megfelelő címkével ellátott alhálózatokkal, amelyek biztonságosak egy Web Application Firewall (WAF) címkével.

- [A fürtelszigetelés ajánlott eljárásai az AKS-ban](operator-best-practices-cluster-isolation.md)

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Az AKS hálózati kapcsolatának és biztonságának ajánlott eljárásai](operator-best-practices-network.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Azure Policy a "Microsoft.ContainerService" névtérben található aliasok használatával egyéni szabályzatokat hozhat létre a Azure Kubernetes Service- (AKS-) példányok konfigurációjának naplózására vagy kényszerítére. Használjon beépített Azure Policy definíciókat.

Példák az AKS beépített szabályzatdefinícióira:

- HTTPS bejövő forgalom kényszerítése Kubernetes-fürtben

- Az engedélyezett IP-címtartományokat a Kubernetes Servicesben kell meghatározni

- Szerepköralapú Access Control (RBAC) kell használni a Kubernetes Servicesben

- Annak biztosítása, hogy csak engedélyezett tárolólemezképek legyenek a Kubernetes-fürtben

Exportálja az AKS-konfiguráció sablonját JavaScript Object Notation (JSON) Azure Resource Manager. Rendszeresen ellenőrizze, hogy ezek a konfigurációk megfelelnek-e a szervezet biztonsági követelményeinek. Az Azure-erőforrások Azure Security Center alapkonfigurációként használhatja az azure-erőforrásokra vonatkozó javaslatokat. 

- [Az AKS-podok biztonsági szabályzatának konfigurálása és kezelése](use-pod-security-policies.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató:** Az Azure Kubernetes-fürtök (AKS-fürtök) biztonsági optimalizált operációs rendszerrel telepített gazdagép virtuális gépeken vannak telepítve. A gazdagép operációs rendszerében további biztonsági intézkedések vannak beépítve a támadási felület csökkentése és a tárolók biztonságos üzembe helyezése érdekében. 

Az Azure napi javításokat (beleértve a biztonsági javításokat) alkalmaz az AKS virtuális gépek gazdagépeire, amelyek némelyike újraindítást igényel. Az ügyfelek felelnek az AKS virtuálisgép-gazdagépek szükség szerint való újraindításának ütemezéséért. 

- [Biztonsági védelem az AKS-ügynökcsomópont gazda operációs rendszerében](security-hardened-vm-host-image.md)

- [Az AKS virtuálisgép-gazdagépek biztonságának megsokosodása](security-hardened-vm-host-image.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** Biztonságossá Azure Kubernetes Service (AKS-) fürtöt podbiztonsági szabályzatokkal.  Korlátozza az ütemezni tervezett podokat a fürt biztonságának növelése érdekében. 

Azok a podok, amelyek nem engedélyezett erőforrásokat kérnek, nem futtathatók az AKS-fürtben. 

Emellett a Azure Policy [deny] és a [deploy if not exist] hatásokkal kényszerítheti az AKS üzemelő példányokkal kapcsolatos Azure-erőforrások (például virtuális hálózatok, alhálózatok, Azure Firewall-tűzfalak, tárfiókok stb.) biztonsági beállításait. 

Hozzon létre Azure Policy definíciókat a következő névterek aliasai használatával: 

- Microsoft.ContainerService

- Microsoft.Network

További információk a hivatkozott hivatkozásokon érhetők el.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató:** Azure Kubernetes Service (AKS)-fürtök a biztonságra optimalizált operációs rendszerrel telepített gazdagép virtuális gépeken vannak telepítve. A gazdagép operációs rendszerében további biztonsági intézkedések vannak beépítve a támadási felület csökkentése és a tárolók biztonságos üzembe helyezése érdekében. 

Tekintse meg a gazdagép operációs rendszerében beépített Center for Internet Security (CIS) vezérlők listáját.  

- [Biztonsági védelem az AKS-ügynökcsomópont gazda operációs rendszerében](security-hardened-vm-host-image.md)

- [Az AKS-fürtök állapotkonfigurációjának](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

- [Az AKS virtuálisgép-gazdagépek biztonságának biztonságával kapcsolatos védelem](security-hardened-vm-host-image.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Egyéni definíciók használata esetén az Azure Repos használatával biztonságosan tárolhatja és kezelheti Azure Policy konfigurációit. Exportálja a Azure Kubernetes Service (AKS) konfigurációjának sablonját JavaScript Object Notation (JSON) Azure Resource Manager. Időnként ellenőrizze, hogy a konfigurációk megfelelnek-e a szervezet biztonsági követelményeinek.

Implementáljon külső megoldásokat, például a Terraformot egy konfigurációs fájl létrehozásához, amely deklarálja a Kubernetes-fürt erőforrásait. Az ajánlott biztonsági eljárások implementációja révén megsokosíthatja az AKS üzembe helyezését, és kódként tárolhatja a konfigurációt egy biztonságos helyen.

- [Kubernetes-fürt meghatározása](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [Biztonsági védelem az AKS-ügynökcsomópont gazda operációs rendszerében](security-hardened-vm-host-image.md)

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató:** Nem alkalmazható a Azure Kubernetes Service (AKS) esetén. Az AKS alapértelmezés szerint a gazdagép biztonságra optimalizált operációs rendszerét (OS) biztosítja. Jelenleg nincs lehetőség alternatív vagy egyéni operációs rendszer kiválasztására.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** A Azure Policy Azure Policy használatával korlátozásokat lehet érvényben helyezni az előfizetésben a "Microsoft.ContainerService" névtér beépített szabályzatdefiníciói, valamint aliasai használatával. 

Egyéni szabályzatok létrehozása a rendszerkonfigurációk naplózása és kényszerítés érdekében. Folyamat és folyamat fejlesztése a szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Konfigurációkezelő eszközök központi telepítése operációs rendszerekhez

**Útmutató:** Azure Kubernetes Service (AKS)-fürtök a biztonságra optimalizált operációs rendszerrel telepített gazdagép virtuális gépeken vannak telepítve. A gazdagép operációs rendszerében további biztonsági intézkedések vannak beépítve a támadási felület csökkentése és a tárolók biztonságos üzembe helyezése érdekében. 

Tekintse meg az AKS-gazdagépekbe beépített Center for Internet Security (CIS) vezérlők listáját.  

- [Biztonsági védelem az AKS-ügynökcsomópont gazda operációs rendszerében](security-hardened-vm-host-image.md)

- [Az AKS virtuálisgép-gazdagépek biztonságának biztonságával kapcsolatos védelem](security-hardened-vm-host-image.md)

- [Az AKS-fürtök állapotkonfigurációjának](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** A Security Center használatával alapkonfiguráció-vizsgálatot végezhet a Azure Kubernetes Service (AKS) üzemelő példányaihoz kapcsolódó erőforrásokon. Ilyenek például az AKS-fürt, az AKS-fürt üzembe helyezéséhez használt virtuális hálózat, a Terraform állapotának nyomon követésére használt Azure Storage-fiók, vagy az AKS-fürt operációs rendszeréhez és adatlemezéhez használt titkosítási kulcsokhoz használt Azure Key Vault-példányok.

- [Javaslatok szervizlének Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Automatikus konfigurációfigyelés megvalósítása operációs rendszerekhez

**Útmutató:** A Security Center (Compute apps) szakaszban található ajánlott tárolók használatával alapkonfiguráció-vizsgálatot végezhet a &amp; Azure Kubernetes Service(AKS-) fürtökön. 

Az irányítópulton értesítést Security Center, ha konfigurációs problémákat vagy biztonsági réseket talál. Ehhez engedélyeznie kell a választható Container Registries csomagot, amely lehetővé Security Center a kép beolvasását.  

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../security-center/container-security.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** Azure Key Vault integrálhatja a Azure Kubernetes Service (AKS) fürtbe egy FlexVolume-meghajtóval. A Azure Key Vault tárolja és rendszeresen váltsa át a titkos kulcsokat, például hitelesítő adatokat, tárfiókkulcsokat vagy tanúsítványokat. A FlexVolume illesztőprogram lehetővé teszi, hogy az AKS-fürt natív módon lekérje a hitelesítő Key Vault és biztonságosan csak a kérelmező pod számára biztosítsa azokat. Egy pod által felügyelt identitással kérjen hozzáférést a Key Vault és kérje le a szükséges hitelesítő adatokat a FlexVolume-illesztőn keresztül. Győződjön meg Key Vault, hogy a Soft Delete engedélyezve van. 

Korlátozza a hitelesítő adatokhoz való kitettséget azáltal, hogy nem határozza meg a hitelesítő adatokat az alkalmazás kódban. 

Kerülje a rögzített vagy megosztott hitelesítő adatok használatát. 

- [Az Azure Kubernetes Service (AKS) alkalmazásainak és fürtjeinek biztonsági fogalmai](concepts-security.md)

- [Az Key Vault használata az AKS-fürtön](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Ajánlott biztonsági eljárásként ne határozza meg a hitelesítő adatokat az alkalmazáskódban. Az Azure-erőforrások felügyelt identitásának használatával a podok bármely azure-beli szolgáltatásban hitelesítheti magukat, beleértve az azure-beli Azure Key Vault. A podhoz egy Azure-identitás van hozzárendelve, amely hitelesíti magát az Azure Active Directory-ban (Azure AD), és digitális jogkivonatot kap, amely bemutatható más Azure-szolgáltatásoknak, amelyek ellenőrzik, hogy a pod jogosult-e a szolgáltatáshoz való hozzáférésre, és végrehajtja a szükséges műveleteket.

Vegye figyelembe, hogy a podok felügyelt identitásai csak Linux-podokkal és tároló-rendszerképekkel használhatók. A Azure Key Vault a digitális kulcsok és hitelesítő adatok tárolására és lekérésre. Az olyan kulcsok, mint az operációsrendszer-lemezek titkosítására használt kulcsok, az AKS-fürtadatok tárolhatók a Azure Key Vault.

A szolgáltatásnév az AKS-fürtökben is használható. A szolgáltatásnévvel használt fürtök azonban idővel olyan állapotba jutnak, amelyben a szolgáltatásnév megújítása a fürt megfelelő állapotának eléréséhez megújulhat. A szolgáltatásnév kezelése összetettebbé teszi a szolgáltatást, ezért egyszerűbb a felügyelt identitások használata. Ugyanezek az engedélykövetelmények vonatkoznak a szolgáltatásnévre és a felügyelt identitásra is.

- [A felügyelt identitások és a Key Vault (AKS) Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

- [Azure AD Pod-identitás](https://github.com/Azure/aad-pod-identity)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódon belül. A Credential Scanner emellett a felderített hitelesítő adatok biztonságosabb helyekre való áthelyezését is támogatja, például Azure Key Vault javaslatokkal.

Korlátozza a hitelesítő adatokhoz való kitettséget azzal, hogy nem határozza meg a hitelesítő adatokat az alkalmazás kódban. és kerülje a megosztott hitelesítő adatok használatát. Azure Key Vault kell használni a digitális kulcsok és hitelesítő adatok tárolására és lekérésre. Az Azure-erőforrások felügyelt identitásának használatával a pod hozzáférést kérhet más erőforrásokhoz. 

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Ajánlott fejlesztői eljárások a podok biztonságával kapcsolatban](developer-best-practices-pod-security.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevők elleni védelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Központilag felügyelt kártevőirtó szoftver használata

**Útmutató:** Az AKS kezeli az ügynökcsomópontok életciklusát és műveleteit az Ön nevében – az ügynökcsomópontokhoz társított IaaS-erőforrások módosítása nem támogatott. A Linux-csomópontok azonban démonkészletekkel telepíthetnek egyéni szoftvereket, például kártevőirtó megoldásokat.

- [Biztonsági riasztások referencia-útmutatója](../security-center/alerts-reference.md)

- [Tárolókra vonatkozó riasztások – Azure Kubernetes Service fürtök](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Az AKS megosztott felelősségi és démonkészletei](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** Az AKS-erőforrásokra feltöltött fájlok előzetes vizsgálatának útmutatója. Az Security Center adatszolgáltatások fenyegetésészlelése segítségével észlelheti a tárfiókba feltöltött kártevőket, ha Azure Storage-fiókot használ adattárként, vagy nyomon követi az AKS-fürt Terraform-állapotát. 

- [Az Azure Security Center adatszolgáltatások fenyegetésészlelése](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Kártevőirtó szoftverek és aláírások frissítésének biztosítása

**Útmutató:** Az AKS kezeli az ügynökcsomópontok életciklusát és műveleteit az Ön nevében – az ügynökcsomópontokhoz társított IaaS-erőforrások módosítása nem támogatott. A Linux-csomópontok azonban démonkészletekkel telepíthetnek egyéni szoftvereket, például kártevőirtó megoldásokat.

- [Biztonsági riasztások referencia-útmutatója](../security-center/alerts-reference.md)

- [Tárolókra vonatkozó riasztások – Azure Kubernetes Service fürtök](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Az AKS megosztott felelősségi és démonkészletei](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres, automatikus biztonságimentás-frissítések biztosítása

**Útmutató:** Az adatok biztonsági létrehozása a tárolótípusnak megfelelő eszközzel, például a Veleróval, amely az állandó kötetek, valamint további fürterőforrások és konfigurációk biztonsági felhasználásával is képes biztonsági adatokat biztonságira. Rendszeresen ellenőrizze a biztonsági másolatok integritását és biztonságát. 

Állapot eltávolítása az alkalmazásokból a biztonsági mentés előtt. Olyan esetekben, ahol ez nem végrehajtásához biztonsági mentést kell végezni az állandó kötetek adatairól, és rendszeresen tesztelni kell a visszaállítási műveleteket az adatok integritásának és a szükséges folyamatoknak az ellenőrzéséhez.

- [Ajánlott tárolási és biztonsági mentési eljárások az AKS-hez](operator-best-practices-storage.md)

- [Ajánlott eljárások az AKS üzletmenet-folytonosságához és vészhelyreállításához](operator-best-practices-multi-region.md)

- [A Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [A Velero beállítása az Azure-ban](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és az ügyfelek által kezelt kulcsok biztonsági mentése

**Útmutató:** Az adatok biztonságimentet a tárolótípusnak megfelelő eszközzel, például a Veleróval, amely képes az állandó kötetek, valamint további fürterőforrások és -konfigurációk biztonsági felhasználásával. 

PowerShell-parancsokkal rendszeresen, automatikus biztonsági mentést Key Vault tanúsítványokról, kulcsokról, felügyelt tárfiókról és titkos kulcsokról. 

- [Tanúsítványok biztonsági Key Vault biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Biztonsági másolat készítése Key Vault kulcsokról](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Felügyelt tárfiókok Key Vault biztonsági mentése](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Titkos kulcsok Key Vault biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [A Azure Backup](/azure/backup/)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Rendszeres időközönként végezze el a tartalmak visszaállítását a Velero Backupban. Szükség esetén tesztelje az elkülönített virtuális hálózatra való visszaállítást.

PowerShell-parancsokkal rendszeresen végrehajtja az Key Vault tanúsítványok, kulcsok, felügyelt tárfiókok és titkos kulcsok visszaállítását.

- [A tanúsítványok Key Vault visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Új kulcsok Key Vault visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Felügyelt tárfiókok Key Vault visszaállítása](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Titkos kulcsok Key Vault visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [Fájlok helyreállítása azure-beli virtuális gépek biztonsági másolatából](/azure/backup/backup-azure-restore-files-from-vm)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Az adatok biztonsági létrehozása a tárolótípusnak megfelelő eszközzel, például a Veleróval, amely az állandó kötetek, valamint további fürterőforrások és konfigurációk biztonsági felhasználásával is képes biztonsági adatokat biztonságira. 

Engedélyezze Soft-Delete a Key Vault, hogy megvédje a kulcsokat a véletlen vagy rosszindulatú törléstől, ha Azure Key Vault (AKS) Azure Kubernetes Service használja a -hez.

- [Az Azure Storage Service Encryption](../storage/common/storage-service-encryption.md)

- [Az Soft-Delete engedélyezése Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálások konfigurálása a Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [A NIST számítógépes biztonsági incidenskezelési útmutatója](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Rangsoroljuk, hogy mely riasztásokat kell először megvizsgálni, és Security Center a riasztások súlyosságát. A súlyosság azon alapul, Security Center mennyire magabiztos a riasztás kiadásához használt eredmény vagy elemzés, valamint hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.
Egyértelműen jelölje meg az előfizetéseket (például éles, nem éles) és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez. Azonosíthatja a gyenge pontokat és hiányosságokat, és szükség szerint módosíthatja az incidensválasz-terveket.

- [Útmutató az IT-tervekhez és -képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha az Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy az ügyfél adatait illetéktelen vagy illetéktelen fél férte hozzá. 

Az incidensek áttekintése a tény után a problémák megoldásának biztosítása érdekében.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** Riasztások Security Center és javaslatok exportálása a folyamatos exportálás funkcióval. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. 

Válassza ki Security Center adat-összekötőt, hogy a riasztásokat Azure Sentinel igényei szerint és a szervezeti üzleti követelmények alapján streamelni.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A Security Center munkafolyamat-automatizálási funkcióját használhatja a biztonsági riasztások és javaslatok "Logic Apps" funkcióval történő automatikus aktiválásához.

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft kötelezettségvállalási szabályait annak biztosításához, hogy a behatolási tesztek ne sértsen meg Microsoft-szabályzatokat. További információk a Microsoft által a Microsoft által felügyelt felhőinfrafra alatt és alkalmazásokon végzett red teaming stratégiáról és végrehajtásról, valamint az élő webhely-behatolási tesztekről a hivatkozott hivatkozásokon.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
