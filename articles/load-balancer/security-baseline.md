---
title: Az Azure biztonsági alapkonfigurációja Azure Load Balancer
description: A Azure Load Balancer alapkonfigurációja eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64e0a8cfcaf00c55038fbe5d1cdc7423b681b690
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589261"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Az Azure biztonsági alapkonfigurációja Azure Load Balancer

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Microsoft Azure Load Balancer. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az azure-beli biztonsági Azure Load Balancer. **Az** adott Azure Load Balancer nem alkalmazható vezérlők ki vannak zárva.

 
Ha meg Azure Load Balancer az Azure-biztonsági teljesítményteszt teljes körű leképezését, tekintse meg a teljes Azure Load Balancer alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Belső Azure Load Balancer használatával csak bizonyos virtuális hálózatokon vagy társviszonyban álló virtuális hálózatokon belüli háttérerőforrásokra lehet forgalmat engedélyezni anélkül, hogy az internet elérhető. Külső hálózat Load Balancer forráshálózattal

Címfordítás (SNAT) a háttérerőforrások IP-címeinek maszkolására a közvetlen internetes kitettség elleni védelem érdekében.

Az Azure kétféle Load Balancer kínál: Standard és Alapszintű. A standard Load Balancer az összes éles számítási feladathoz. Implementálja a hálózati biztonsági csoportokat, és csak az alkalmazás megbízható portjaihoz és IP-címtartományaihoz engedélyezze a hozzáférést. Ha nincs hozzárendelve hálózati biztonsági csoport a háttér virtuális gépek háttér-alhálózatához vagy hálózati adaptere számára, a terheléselosztásból nem engedélyezett az ezekhez az erőforrásokhoz való forgalom. A Standard Load Balancerekkel kimenő szabályokat biztosít a kimenő NAT hálózati biztonsági csoporttal való meghatározásához. Tekintse át ezeket a kimenő szabályokat a kimenő kapcsolatok viselkedésének finomhangolásához.

Éles számítási standard Load Balancer esetében ajánlott a standard Load Balancer használata, és általában az Alapszintű Load Balancer-t csak tesztelésre használják, mivel az alaptípus alapértelmezés szerint nyitva van az internetről származó kapcsolatok számára, és nem igényel hálózati biztonsági csoportokat a működéshez.

- [Kimenő kapcsolatok az Azure-ban](load-balancer-outbound-connections.md)

- [Az Azure nyilvános Load Balancer](upgrade-basic-standard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati hálózatok konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** A Load Balancer egy átmenő szolgáltatás, mivel a háttérerőforrásokra alkalmazott hálózati biztonsági csoportok szabályaira és az internet-hozzáférés szabályozására konfigurált kimenő szabályokra támaszkodik.

Tekintse át a standard Load Balancer konfigurált kimenő szabályokat a Load Balancer Kimenő szabályok panelen és a terheléselosztási szabályok panelen, ahol az Implicit kimenő szabályok engedélyezve lehetnek.

Figyelje a kimenő kapcsolatok számát annak nyomon követéséhez, hogy az erőforrások milyen gyakran érik el az internetet. 

Használja Security Center, és kövesse a hálózatvédelmi javaslatokat az Azure-beli hálózati erőforrások biztonságának biztosítása érdekében.

Kövesse a háttérerőforrásokra vonatkozó biztonsági javaslatokat, engedélyezze a hálózati biztonsági csoportok forgalomnaplóit, és küldje el a naplókat egy Azure Storage-fiókba naplózásra.

Továbbá küldje el a forgalmi naplókat egy Log Analytics-munkaterületre, majd Traffic Analytics segítségével betekintést nyújt az Azure-felhő forgalmi mintáiba. A hálózati Traffic Analytics közé tartozik a hálózati tevékenységek vizualizációja, a gyors helyek és a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati helytelen konfigurációk azonosítása.

- [Hálózati biztonsági csoport forgalomnaplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Hogyan kimenő kapcsolat statisztikáinak megtekintése](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** Explicit módon határozza meg az internetkapcsolatot és az érvényes forrás IP-címeket a kimenő szabályokon és hálózati biztonsági csoportokon keresztül a Load Balancer segítségével a Microsoft fenyegetési intelligenciáját a webalkalmazások védelméhez.

- [A Azure Firewall](../firewall/integrate-lb.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Az Azure elosztott szolgáltatásmegtagadásos (DDoS) standard szintű védelmének engedélyezése az Azure Virtual Network a DDoS-támadások ellen. 

A Azure Firewall a szervezet minden hálózati határán, engedélyezett fenyegetésintelligencia-alapú szűréssel, és "Riasztás és megtagadás" beállítással konfigurálva a kártékony hálózati forgalomhoz.

 

A Security Center fenyegetések elleni védelemmel észlelheti az ismert kártékony IP-címekkel való kommunikációt. 

A standard Load Balancer úgy lett kialakítva, hogy alapértelmezés szerint biztonságos legyen, és egy privát és elkülönített Virtual Network. A rendszer csak akkor nyitja meg a bejövő forgalmat, ha a hálózati biztonsági csoportok megnyitják az engedélyezett forgalmat, és nem engedélyezik az ismert kártékony IP-címeket. Ha a virtuális gép erőforrásának egy alhálózatán vagy hálózati adapterében nincs hálózati biztonsági csoport a virtuális gép Load Balancer, a forgalom nem érje el ezt az erőforrást. 

A Azure Firewall a szervezet minden hálózati határán, engedélyezett fenyegetésintelligencia-alapú szűréssel, és "Riasztás és megtagadás" beállítással konfigurálva a kártékony hálózati forgalomra, hogy megakadályozza a rosszindulatú IP-címekről történő támadásokat. 

Útmutató implementálja a Azure Firewall és a Load Balancer.

A Security Center védelmi funkciók használatával észlelheti az ismert kártékony IP-címekkel való kommunikációt. 

Security Center (Standard szint) a virtuális gépekhez való, megfelelő időben történő hozzáférést biztosít, és úgy konfigurálja az engedélyezett forrás IP-címeket, hogy csak jóváhagyott IP-címekről/tartományokból engedélyezzenek hozzáférést.
 

Az Security Center adaptív hálózatkorlátozási funkciójával olyan hálózati biztonsági csoportkonfigurációkat javasolhat, amelyek a tényleges forgalom és fenyegetésintelligencia alapján korlátozzák a portokat és a forrás IP-eket.
 

- [A Azure DDoS Protection Standard kezelése a Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall veszélyforrás-felderítésen alapuló szűrés](../firewall/threat-intel.md)

- [Fenyegetésvédelem az Azure Security Centerben](../security-center/azure-defender.md)

- [Felügyeleti portok védelme igény szerinti hozzáféréssel](../security-center/security-center-just-in-time.md)

- [Adaptív hálózat-Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [A Azure Firewall integrálása a Load Balancer](../firewall/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** A Network Watcher tevékenységek vizsgálatához engedélyezze a csomagrögzítést.

- [Új Network Watcher létrehozása](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Implementálja a Azure Marketplace olyan ajánlatot, amely támogatja az IDS/IPS funkciót, és hasznos vizsgálatot biztosít a felhasználói Load Balancer. 

Akkor Azure Firewall fenyegetési intelligenciát, ha a hasznos vizsgálatot nem követelmény. Azure Firewall veszélyforrás-felderítésen alapuló szűrés az ismert kártékony IP-címek és tartományok be- és/vagy letiltásán alapul. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Telepítse a választott tűzfalmegoldást a szervezet minden hálózati határán a rosszindulatú forgalom észlelése és/vagy blokkolása érdekében.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [A virtuális gép Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása a Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** Explicit módon határozza meg az internetkapcsolatot és az érvényes forrás IP-címeket kimenő szabályokon és hálózati biztonsági csoportokon keresztül a Load Balancer segítségével, hogy a Microsoft fenyegetés-felderítési funkcióival védjék a webalkalmazásokat.

- [A Azure Firewall](../firewall/integrate-lb.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Szolgáltatáscímkék használata adott IP-címek helyett biztonsági szabályok létrehozásakor. A megfelelő szolgáltatás forgalmának engedélyezése vagy megtagadása érdekében adja meg a szolgáltatáscímke nevét a szabály forrás- vagy célmezőben. 

A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkéket a címek változásával. 

Alapértelmezés szerint minden hálózati biztonsági csoport tartalmazza az AzureLoadBalancer szolgáltatáscímkét, amely engedélyezi az állapot-mintavételi forgalmat. 

A hálózati biztonsági csoportok szabályaiban használható összes szolgáltatáscímkét az Azure dokumentációjában találhatja meg.

- [Elérhető szolgáltatáscímkék](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Szabványos biztonsági konfigurációk definiálása és megvalósítása hálózati erőforrásokhoz Azure Policy.

Az Azure Blueprints segítségével leegyszerűsítheti a nagy méretű Azure-beli üzembe helyezéseket, mivel egyetlen tervdefinícióba csomagolásával egyetlen tervdefinícióba használhatja az olyan kulcsfontosságú környezeti összetevőket, mint az Azure Resources Manager-sablonok, az Azure RBAC-vezérlők és -szabályzatok. 

Alkalmazza a tervet az új előfizetésekre, és finomhangolja a vezérlést és a felügyeletet a verziószámozáson keresztül.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy mintákat a hálózatépítéshez](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprints-terv létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Erőforráscímkék használata hálózati biztonsági csoportokhoz és más, a hálózati biztonsághoz és a forgalomhoz kapcsolódó erőforrásokhoz. 

A "Description" (Leírás) mezőben dokumentálni lehet a hálózati biztonsági csoportok egyes szabályaira vonatkozó, a hálózatokra és hálózatokról be- és visszaforgalmat engedélyező szabályokat.

Implementálja a címkézéshez kapcsolódó beépített Azure Policy-definíciókat, például a "Címke és az érték megkövetelése" definíciót, amely biztosítja, hogy minden erőforrás címkékkel együtt létezik, és értesítést küld a meglévő, címkézetlen erőforrásokról.

Az Azure PowerShell vagy az Azure CLI használatával erőforrásokat keres vagy hajt végre a címkék alapján.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Azure-beli virtuális Virtual Network](../virtual-network/quick-create-portal.md)

- [Hálózati forgalom szűrése hálózati biztonsági csoport szabályokkal](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével monitorozható az erőforrás-konfiguráció, és észlelhetők az Azure-erőforrások változásai. 

Hozzon létre riasztásokat a Azure Monitor, amelyek értesítik, ha kritikus fontosságú erőforrások módosulnak.

- [Azure-tevékenységnapló eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Tekintse át a terheléselosztás kimenő szabályainak és hálózati biztonsági csoportjainak módosításait az előfizetések tevékenységnaplójában. 

Tekintse meg a belső gazdagépnaplókat, és győződjön meg arról, hogy a háttérerőforrások biztonságosak.

Exportálja ezeket a naplókat a Log Analyticsbe vagy egy másik tárolási platformra. A Azure Monitor Log Analytics-munkaterületek használatával végezhet lekérdezést és elemzést, az Azure Storage-fiókokat pedig hosszú távú és archiválási tárterületre használhatja.

Az adatok engedélyezésével és beállításával Azure Sentinel külső SIEM-et a szervezeti üzleti követelmények alapján.

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Platformnaplók és -metrikák gyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure-beli virtuális gép belső gazdagépnaplóinak gyűjtése a Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Első lépések a Azure Monitor külső SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Platformtevékenység-naplók](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Tekintse át az alapszintű felügyeleti naplók tevékenységnaplóiban rögzített vezérlő- és felügyeleti Load Balancer. Ezek a rögzítési beállítások alapértelmezés szerint engedélyezve vannak. 

A Tevékenységnaplók segítségével figyelheti az erőforrásokon végzett műveleteket az összes tevékenység és azok állapotának megtekintéséhez. 

A tevékenységnaplók segítségével állapítsa meg, milyen műveleteket végzett az előfizetés erőforrásain: 

- ki indította el a műveletet
- a művelet beestekor
- a művelet állapota

- egyéb tulajdonságok értékei, amelyek segíthetnek a művelet kutatásában

Információk lekérése a tevékenységnaplóból a Azure PowerShell, az Azure parancssori felület (CLI), az Azure REST API vagy a Azure Portal. 

Implementálja a többdimenziós diagnosztikát a standard Load Balancer konfigurálási képességekhez a Azure Monitor.  Ezek közé tartoznak a biztonsághoz elérhető metrikák, például a forráshálózati címfordítás (SNAT) kapcsolataira és portjaira vonatkozó információk. További metrikák is elérhetők a SYN (szinkronizálási) csomagokhoz és csomagszámlálókhoz. 

Többdimenziós metrikák lekérése programozott módon API-k segítségével, és azok írása egy tárfiókba az "Összes metrika" lehetőséggel.

Az Azure Audit Logs tartalomcsomag és a Microsoft Power BI az adatokat előre konfigurált irányítópultokkal elemezheti, vagy igény szerint testre szabhatja a nézeteket.

Naplók streamelése eseményközpontba vagy Log Analytics-munkaterületre. Az Azure Blob Storage-ból is kinyerheti őket, és különböző eszközökben, például az Excelben és a Power BI. 

Az üzleti követelmények alapján engedélyezheti és Azure Sentinel külső SIEM-et, és adatokat is bevet.

- [Tekintse át ezt a cikket az Audit operations with Resource Manager (Naplózási műveletek a következővel) cikkben részletesen Resource Manager](../azure-resource-manager/management/view-activity-logs.md)

- [Az Azure Monitor naplózza a nyilvános Alapszintű Load Balancert](load-balancer-monitor-log.md)

- [Tevékenységnaplók megtekintése az erőforrásokon végzett műveletek figyelése érdekében](../azure-resource-manager/management/view-activity-logs.md)

- [Többdimenziós metrikák programozott lekérése API-k segítségével](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Első lépések a Azure Monitor külső SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági naplók tárolásának megőrzésének konfigurálása

**Útmutató:** A tevékenységnapló alapértelmezés szerint engedélyezve van, és 90 napig megmarad az Azure Eseménynaplók tárolójában. Állítsa be a Log Analytics-munkaterület megőrzési időszakát a szervezet megfelelőségi Azure Monitor. Az Azure Storage-fiókokat hosszú távú és archiválási tárterülethez használhatja.

- [Tevékenységnaplók megtekintése az erőforrásokon végzett műveletek figyelése érdekében](../azure-resource-manager/management/view-activity-logs.md)

- [Az adatmegőrzési időtartam módosítása a Log Analyticsben](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fióknaplókhoz](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Erőforrások figyelése, kezelése és standard Load Balancer hibaelhárítása a Load Balancer lap Azure Portal és a Resource Health lap Azure Monitor. A biztonsághoz elérhető metrikák közé tartoznak a forráshálózati címfordítási (SNAT-) kapcsolatokra és portokra vonatkozó információk. A SYN-csomagok és csomagszámlálók mérőszámai is elérhetők. 

A Azure Monitor a végpontállapot-mintavétel állapotának áttekintésére többdimenziós metrikák használatával Standard, külső és belső Terheléselosztás esetén. Gyűjtse össze ezeket a metrikákat programozott módon API-kon keresztül, és írja őket egy tárfiókba az "Összes metrika" lehetőséggel.

Azure Monitor alapszintű belső terheléselosztáshoz nem érhetők el a naplók. 

Az Azure Monitor használatával az alapszintű külső Load Balancer háttérkészletenként összegezve láthatja az állapot-mintavétel állapotát, például azt, hogy a háttérkészletben hány példány nem kap kéréseket a Load Balancer-től az állapot-mintavételi hibák miatt. 

Implementálja a naplózást az Azure Operational Insights segítségével, hogy statisztikákat biztosítson a terheléselelosztás állapotával kapcsolatban. 

A tevékenységnaplók segítségével megtekintheti a riasztásokat, és figyelheti az erőforrásokra vonatkozó műveleteket és azok állapotát az Azure-előfizetésében. A tevékenységnaplók alapértelmezés szerint engedélyezve vannak, és megtekinthetők a Azure Portal. 

A Microsoft Power BI Azure-auditnaplók tartalomcsomaggal együtt használhatja, és előre konfigurált irányítópultokkal elemezheti az adatait. Az üzleti igényeknek megfelelően testre szabhatja a nézeteket. 

Naplók streamelése eseményközpontba vagy Log Analytics-munkaterületre. Az Azure Blob Storage-ból is kinyerheti őket, és különböző eszközökben, például az Excelben és a Power BI. Engedélyezheti és bevetheti az adatokat Azure Sentinel külső SIEM-hez.

- [A Load Balancer állapotadat-mintavételei](load-balancer-custom-probe-overview.md)

- [Azure Monitor REST API](/rest/api/monitor)

- [Metrikák lekérése REST API](/rest/api/monitor/metrics/list)

- [standard Load Balancer metrikák, riasztások és erőforrás-állapot diagnosztikával való ellátása](load-balancer-standard-diagnostics.md)

- [Az Azure Monitor naplózza a nyilvános Alapszintű Load Balancert](load-balancer-monitor-log.md)

- [A terheléselosztási metrikák megtekintése a Azure Portal](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** A Security Center Log Analytics-munkaterülettel való használatával monitorozást és riasztásokat használhat a biztonsági naplókban és eseményekben Load Balancer tevékenységekkel kapcsolatos rendellenes tevékenységekről.

Engedélyezi és beveszi az adatokat egy Azure Sentinel SIEM-eszközbe.

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése a Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a Log Analytics-naplóadatokról](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szerepkör-hozzárendelésekkel lehetővé teszi az Azure-erőforrások, például a Load Balancer hozzáférésének kezelését. Rendelje hozzá ezeket a szerepköröket felhasználókhoz, csoportokhoz szolgáltatásnévhez és felügyelt identitásokhoz.

Előre definiált és beépített szerepkörök leltározása bizonyos erőforrásokhoz olyan eszközökkel, mint az Azure CLI, Azure PowerShell vagy a Azure Portal.

- [Címtárszerepkomó le Azure Active Directory (Azure AD) a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Security Center identitás- és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomások (PAW) használata az Azure hálózati erőforrások kezeléséhez és eléréséhez konfigurált többtényezős hitelesítéssel. 

- [Tudnivalók a Privileged Access munkaállomásról](/security/compass/privileged-access-devices)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés nevestű helyek használatával csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításai számára engedélyezi a hozzáférést.

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata a szolgáltatások központi hitelesítési és engedélyezési rendszereként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt, valamint a sót, a hashe-eket, valamint biztonságosan tárolja a felhasználói hitelesítő adatokat.  

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** A Azure Active Directory (Azure AD) segítségével naplókat biztosít az elavult fiókok felderítésének segítésekor. 

Az Azure-identitások hozzáférési felülvizsgálatai a csoporttagság, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések hatékony kezeléséhez hajthatóak végre. A felhasználói hozzáférést rendszeresen át kell vizsgálni, hogy csak az aktív felhasználók tudjanak folyamatos hozzáférést biztosítani.

- [Az Azure AD-jelentéskészítés](/azure/active-directory/reports-monitoring/)

- [Az Azure Identity Hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Azure Active Directory (Azure AD) bejelentkezési tevékenységének, naplózási és kockázati eseménynapló-forrásainak integrálása bármilyen SIEM- vagy monitorozási eszközzel a hozzáférés alapján.

Egyszerűsítheti ezt a folyamatot az Azure AD felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint az auditnaplók és bejelentkezési naplók Log Analytics-munkaterületre való küldésével. A kívánt riasztások a Log Analytics-munkaterületen konfigurálhatóak.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Azure Active Directory (Azure AD) Kockázat- és Identity Protection-funkcióival konfigurálhatja az automatikus válaszokat a felhasználói identitásokkal kapcsolatos gyanús műveletekre. A további vizsgálatokhoz Azure Sentinel adatokat a rendszernek.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek egyeztetni a TLS 1.2-t vagy annál nagyobbat.

Kövesse Azure Security Center az átvitel közbeni, valamint az átvitel közbeni titkosításra vonatkozó javaslatokat.

- [Az átvitel során az Azure-ral történő titkosítás](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Az Erőforrásokhoz való hozzáférés kezelése az Azure RBAC használatával

**Útmutató:** Az Azure RBAC használatával szabályozhatja a hozzáférését Load Balancer erőforrásokhoz.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató:** Load Balancer egy átmenő szolgáltatás, amely nem tárol ügyféladatokat. Ez a Microsoft által felügyelt mögöttes platform része. 

A Microsoft minden ügyféltartalmat bizalmasként kezel, és nagy terjedelmüknek megfelelő védelmet kínál az ügyfelek adatvesztése és kitettsége ellen. 

Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Monitor Azure-tevékenységnaplóval használva riasztásokat hozhat létre, ha kritikus Fontosságú Azure-erőforrásokon, például a fontos éles számítási feladatokhoz használt terheléselosztáson történik módosítások.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetések összes erőforrását (például számítás, tárolás, hálózat, portok, protokollok stb.). Azure Resource Manager az aktuális erőforrások létrehozásához és használathoz ajánlott.

Győződjön meg arról, hogy megfelelő (olvasási) engedélyeket ad meg a bérlőben, és számba veszi az előfizetések összes Azure-előfizetését és -erőforrását.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása Azure-erőforrásokra metaadatokkal, hogy logikailag rendszerezzen egy taxonómia szerint.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az eszközök rendszerezéséhez és nyomon követéséhez használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket, ha szükséges. 

Rendszeresen egyeztetni kell a leltárt, és gondoskodni kell arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások készletének meghatározása és karbantartása

**Útmutató:** Létrehozhatja a jóváhagyott Azure-erőforrások listáját a szervezeti igényeknek megfelelően, amelyeket engedélyezési mechanizmusként használhat. Ez lehetővé teszi a szervezet számára az újonnan elérhető Azure-szolgáltatások felvételét azt követően, hogy hivatalosan áttekintik és jóváhagyják a szervezet szokásos biztonsági értékelési folyamatai.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy használatával korlátozásokat korlátozhat az előfizetésében létrehozható erőforrások típusára vonatkozóan.

Lekérdezheti és felderítheti a saját előfizetésben Azure Resource Graph erőforrásokat. 

Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Azure Policy-minta beépített virtuális hálózatokhoz](/azure/virtual-network/policy-samples)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** A Azure Active Directory (Azure AD) feltételes hozzáféréssel korlátozhatja a felhasználók Azure Resource Manager-val való interakcióját a "Hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása az Azure Resources Managerhez való hozzáférés letiltása érdekében](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Magas kockázatú alkalmazások fizikai vagy logikai elkülönítése

**Útmutató:** Az üzleti műveletekhez szükséges, de a szervezet számára nagyobb kockázatot jelentő szoftvereket el kell különíteni a saját virtuális gépüktől és/vagy virtuális hálózatuktól, és megfelelően biztonságosnak kell lenniük egy Azure Firewall-val vagy egy hálózati biztonsági csoporttal.

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)

- [Hálózati biztonsági csoport létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások konfigurációjának naplózására vagy kényszerítére. Használjon beépített Azure Policy definíciókat.

Azure Resource Manager képes exportálni a sablont az JavaScript Object Notation -ban (JSON), amelyet át kell vizsgálni annak biztosítása érdekében, hogy a konfigurációk megfelelnek a szervezet biztonsági követelményeinek.

Exportálja Azure Resource Manager sablonokat JavaScript Object Notation (JSON) formátumokba, és rendszeresen ellenőrizze, hogy a konfigurációk megfelelnek-e a szervezeti biztonsági követelményeknek.

A javaslatok Security Center az Azure-erőforrások biztonságos alapkonfigurációjaként.

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez](../governance/policy/tutorials/create-and-manage.md)

- [Egy- és többerőforrásos exportálás sablonba a Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** A Azure Policy [deny] és a [deploy if not exist] használatával kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon.  Emellett a Azure Resource Manager is használhatja a szervezet által megkövetelt Azure-erőforrások biztonsági konfigurációjának fenntartásához. 

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Az Azure DevOps használatával biztonságosan tárolhatja és kezelheti a kódot, például egyéni Azure Policy definíciókat, Azure Resource Manager sablonokat és a célállapot-konfigurációs szkripteket.

Engedélyeket adhat vagy tagadhat meg adott felhasználóknak, beépített biztonsági csoportoknak vagy az Azure Active Directory-ben (Azure AD)-ben meghatározott csoportoknak, ha az integrálva van az Azure DevOps-ral, vagy az Azure AD-ben, ha integrálva van a TFS-sel.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure DevOps engedélyeinek és csoportjainak](/azure/devops/organizations/security/about-permissions)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Standard biztonsági konfigurációk definiálása és implementációja Az Azure-erőforrásokhoz a Azure Policy.  Az Azure Policy használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy kényszerítéshez. Implementálja az adott erőforráshoz kapcsolódó beépített szabályzatdefiníciókat Azure Load Balancer erőforrásokhoz.  A konfigurációs módosítások Azure Automation is használhatja. a Azure Load Balancer erőforrásokhoz.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** A Security Center az Azure-erőforrások alapkonfiguráció-vizsgálatának elvégzéséhez, Azure Policy az erőforrás-konfigurációk riasztására és naplózására.

- [Javaslatok szervizlének Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig. 

- [Munkafolyamat-automatizálások konfigurálása a Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Útmutató saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Az ügyfelek az NIST számítógépes biztonsági incidenskezelési útmutatóját is kihasználva segíthetnek a saját incidenskezelési tervük létrehozásában](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így fontossági sorrendbe adhatja, hogy mely riasztásokat kell először megvizsgálni. 

A súlyosság azon alapul, Security Center mennyire magabiztos a riasztás kiadásához használt eredmény vagy elemzés, valamint a riasztáshoz vezető tevékenység mögötti rosszindulatú szándék megbízhatósági szintje.

Jelölje meg az előfizetéseket címkék használatával, és hozzon létre egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatokat feldolgozók számára.  

Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és hiányosságokat, majd szükség szerint módosítsa a választervet. 

- [Az NIST kiadványa – Útmutató az it-csomagokhoz és képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy illetéktelen vagy jogosulatlan fél fért hozzá adataihoz. Tekintse át az incidenseket a probléma megoldása érdekében. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A Security Center riasztások és javaslatok exportálása a folyamatos exportálási funkcióval az Azure-erőforrásokat érintő kockázatok azonosítása érdekében. 

Használja a Folyamatos exportálás funkciót a Security Center, amely lehetővé teszi a riasztások és javaslatok manuális vagy folyamatos exportálását. 

Az Security Center adat-összekötővel streamelhet riasztásokat a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** Az Azure-erőforrások védelme érdekében a Security Center munkafolyamat-automatizálási szolgáltatásával automatikusan kiválthatja a biztonsági riasztások és javaslatok válaszait.

- [Munkafolyamat-automatizálás konfigurálása a Biztonsági beállításban](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft Cloud behatolástesztelési szabályait annak ellenőrzéséhez, hogy a behatolási tesztek nem sértik-e meg a Microsoft szabályzatát. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
