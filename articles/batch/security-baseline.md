---
title: Azure biztonsági alapkonfiguráció a Batch szolgáltatáshoz
description: A Batch biztonsági alapkonfiguráció eljárási útmutatást és erőforrásokat biztosít az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d8ef3efcda55868abec188c10ef904ae40a56722
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728144"
---
# <a name="azure-security-baseline-for-batch"></a>Azure biztonsági alapkonfiguráció a Batch szolgáltatáshoz

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) a Batch-re vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalmat az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** , valamint a Batch-re vonatkozó kapcsolódó útmutatás szerint csoportosítjuk. A Batch szolgáltatásra nem alkalmazható **vezérlők** ki lettek zárva.

 
Ha szeretné megtudni, hogy a Batch hogyan térképezi fel teljesen az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes batch biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: Azure batch készlet (ek) üzembe helyezése egy virtuális hálózaton belül. Ahhoz, hogy a készlet számítási csomópontjai biztonságosan kommunikáljanak más virtuális gépekkel vagy helyszíni hálózattal, egy Azure-beli virtuális hálózat alhálózatában is kiépítheti a készletet. Emellett a készletet a virtuális hálózaton belül is üzembe helyezheti, így szabályozhatja az egyes csomópontok hálózati adapterei (NIC) és az alhálózatok biztonságossá tételéhez használt hálózati biztonsági csoportot (NSG). Konfigurálja úgy a NSG, hogy csak a megbízható IP-/Locations érkező adatforgalmat engedélyezze az interneten.

Ha lehetséges, tiltsa le a nyilvános hálózati hozzáférést az Azure Private link használatával, hogy egy privát végponton keresztül kapcsolódjon a Azure Batch-fiókhoz. Az Azure Private link szolgáltatás védett, és csak hitelesített és engedélyezett privát végpontokról fogad kapcsolatokat. A kapcsolat és a felderíthetőség korlátozásával emellett a Batch-készletben lévő számítási csomópontok nyilvánosan elérhető RDP/SSH-végpontok letiltásával is korlátozható.

- [Azure Batch-készlet létrehozása egy Virtual Networkon belül](batch-virtual-network.md)

- [Hálózati hozzáférés letiltott Azure Batch fiók létrehozása](private-connectivity.md)

- [Privát végpont létrehozása](../private-link/create-private-endpoint-portal.md)

- [Az RDP-/SSH-forgalomhoz való hozzáférés megtagadása](pool-endpoint-configuration.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Batch-készlethez társított virtuális hálózattal/hálózati biztonsági csoporttal (NSG) kapcsolatos hálózati védelmi javaslatok Azure Security Center használata és szervizelése. Engedélyezze a flow-naplókat a Batch-készlet védetté tételéhez használt NSG, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához. NSG-naplókat is küldhet egy Azure Log Analytics-munkaterületre, és az Azure Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. Az Azure Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati hibás konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure DDoS (Distributed szolgáltatásmegtagadás) standard szintű védelem engedélyezése a virtuális hálózaton, amely megvédi Azure batch-készletét a DDOS-támadások elleni védelemhez. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: engedélyezze a flow-naplókat a Azure batch készletének védelméhez használt hálózati biztonsági csoporton (NSG), és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: Ha megfelelőségi célokra van szükség, válasszon ki egy hálózati virtuális berendezést az Azure piactéren, amely támogatja a betöltési észlelési rendszerek (azonosítók) és a behatolás-megelőzési rendszerek (IP-címek) funkcióit.

Ha a betörési észlelés és/vagy a tartalom-ellenőrzésen alapuló megelőzés nem követelmény, akkor az Azure Firewall a veszélyforrások felderítésére használható. Azure Firewall fenyegetés intelligencián alapuló szűréssel riasztást kaphat, és megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Azure Firewall üzembe helyezése egy nyilvános IP-címmel, ugyanabban a virtuális hálózatban, mint a Azure Batch Pool-csomópontok. Konfigurálja a hálózati címfordítási (NAT-) szabályokat az interneten található megbízható helyek és az egyes készlet-csomópontok magánhálózati IP-címei között. A Azure Firewall a veszélyforrások felderítése területen állítsa be a "riasztás és megtagadás" beállítást, hogy megakadályozza a riasztást, és blokkolja az ismert kártékony IP-címekre és tartományokra irányuló forgalmat. Az IP-címek és tartományok a Microsoft Threat Intelligence-hírcsatornából származnak, és csak a legmagasabb szintű megbízhatósági rekordok tartoznak ide. 

- [Azure Batch-készlet létrehozása egy Virtual Networkon belül](batch-virtual-network.md)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a virtuális hálózati szolgáltatás címkéi segítségével definiálhatja a hálózati biztonsági csoportokhoz vagy a Azure batch-készlet (ek) hez társított Azure-tűzfalakhoz tartozó hálózati hozzáférés-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Azure batch készlet (ek) hoz tartozó hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft.BatCH" és a "Microsoft. Network" névterekben, hogy egyéni házirendeket hozzon létre a Azure Batch-készletek hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: az Azure batch-készletekhez társított hálózati biztonsággal és adatforgalommal kapcsolatos címkék használata a hálózati szolgáltatási csoportokhoz (NSG) és az egyéb erőforrásokhoz. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása](../virtual-network/tutorial-filter-network-traffic.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure batch-készletekhez kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log) 

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a Azure batch fiókja Azure monitor a fürtözött eszközök által generált biztonsági adatokat összesítve. Az egyéni lekérdezések használatával észlelheti és reagálhat a környezetbeli fenyegetésekre.  Az erőforrás-szintű figyelés Azure Batch a Batch API-k segítségével figyelheti vagy lekérdezheti az erőforrások állapotát, beleértve a feladatokat, a feladatokat, a csomópontokat és a készleteket.

- [Azure Batch fiók előkészítése Azure Monitor](batch-diagnostics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: Azure batch fiók szintű figyeléshez az egyes batch-fiókokat a Azure monitor szolgáltatásainak használatával figyelheti. Azure Monitor a Batch-fiók szintjén (például készletekben, feladatokban és feladatokban) lévő erőforrások mérőszámait és opcionálisan diagnosztikai naplóit gyűjti. Az adatok gyűjtése és felhasználása manuálisan vagy programozott módon a Batch-fiókban lévő tevékenységek figyeléséhez és a problémák diagnosztizálásához.

Az erőforrás-szint figyeléséhez használja a Azure Batch API-kat az erőforrások állapotának figyeléséhez vagy lekérdezéséhez, beleértve a feladatokat, a feladatokat, a csomópontokat és a készleteket. Azure Batch

- [Azure Batch fiók szintű figyelés és naplózás konfigurálása](monitoring-overview.md)

- [A Batch erőforrás-szintű figyelésének megismerése](monitoring-overview.md#batch-resource-monitoring)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

#### <a name="azure-policy-built-in-definitions"></a>Beépített definíciók Azure Policy

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: a Azure Monitor a Azure batch-fiók erőforrásaihoz tartozó mérőszámokat és diagnosztikai naplókat gyűjt. Az adatok gyűjtését és felhasználását többféleképpen is megfigyelheti Azure Batch-fiókja és a problémák diagnosztizálásához. A metrikai riasztásokat úgy is konfigurálhatja, hogy értesítést kapjon, ha egy metrika eléri a megadott értéket.

Ha szükséges, az egyes készlet-csomópontokhoz biztonságos rendszerhéj (SSH) vagy RDP protokoll (RDP) használatával kapcsolódhat a helyi operációsrendszer-naplók eléréséhez.

- [Diagnosztikai naplók összegyűjtése a Azure Batch-fiókból](batch-diagnostics.md#batch-diagnostics)

- [Távoli kapcsolódás a Azure Batch Pool-csomópontokhoz](./batch-service-workflow-features.md#basic-workflow)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure batch fiók beléptetése Azure monitorra. Győződjön meg arról, hogy a használt Azure Log Analytics munkaterület rendelkezik a szervezet megfelelőségi előírásai szerint beállított napló megőrzési időtartamával

- [Azure Batch figyelés és naplózás konfigurálása](monitoring-overview.md)

- [Az Azure Log Analytics munkaterület megőrzési időtartamának konfigurálása](../azure-monitor/logs/manage-cost-storage.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: Azure batch metrikai riasztások létrehozása, amelyek akkor aktiválódnak, ha egy adott metrika értéke átlép egy adott küszöbértéket.

- [Azure Batch metrikai riasztások konfigurálása](batch-diagnostics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Azure batch metrikai riasztások létrehozása, amelyek akkor aktiválódnak, ha egy adott metrika értéke átlép egy adott küszöbértéket.

- [Azure Batch metrikai riasztások konfigurálása](batch-diagnostics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: a Windows Defender használata az egyes batch-csomópontokon a Windows operációs rendszerek esetében, illetve a Linux használata esetén saját kártevő szoftvereket is biztosíthat.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: harmadik féltől származó megoldás implementálása a DNS-naplózáshoz.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: a konzol naplózásának és a PowerShell átírásának manuális konfigurálása a csomópontok alapján.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure batch-készlet üzembe helyezése során létrehozott helyi rendszergazdai fiók, valamint a létrehozott egyéb fiókok nyilvántartásának fenntartása. Továbbá, ha Azure Active Directory-integrációt használ, az Azure AD olyan beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és ezért lekérdezhető. Az Azure AD PowerShell-modul használatával ad hoc lekérdezéseket a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

Emellett Azure Security Center identitás-és hozzáférés-kezelési javaslatokat is használhat.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Identitás és hozzáférés figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure batch-készlet kiépítés esetén a helyi számítógép-fiókok létrehozására van lehetőség. Nincsenek módosítható alapértelmezett jelszavak, azonban a Secure Shell-(SSH-) és a RDP protokoll-(RDP-) hozzáféréshez eltérő jelszavakat is megadhat. Azure Batch készlet konfigurálása után létrehozhat egy véletlenszerű felhasználót az egyes csomópontokhoz a Azure Portal vagy a Azure Resource Manager API-n keresztül.

- [Felhasználó hozzáadása adott számítási csomóponthoz](/rest/api/batchservice/computenode/adduser)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: Azure batch-alkalmazások hitelesítésének integrálása Azure Active Directory használatával. Szabályzatokat és eljárásokat hozhat létre a dedikált rendszergazdai fiókok használata körül.

Emellett Azure Security Center identitás-és hozzáférés-kezelési javaslatokat is használhat.

- [Batch-alkalmazások hitelesítése Azure Active Directory](batch-aad-auth.md)

- [Identitás és hozzáférés figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: Azure batch-alkalmazások hitelesítésének integrálása Azure Active Directory használatával. Engedélyezze az Azure AD többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

 

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: a mancsok (emelt szintű hozzáférésű munkaállomások) használata többtényezős hitelesítéssel, amely a Azure batch-erőforrások bejelentkezésére és konfigurálására van konfigurálva.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: ha a Azure batch-alkalmazások integrált hitelesítése Azure Active Directory, a naplók és a riasztások generálásához Azure Active Directory biztonsági jelentéseket kell használni, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: ha a Azure batch-alkalmazások integrált hitelesítése Azure Active Directory-mel rendelkezik, használhatja a feltételes hozzáférést elnevezett helyekkel, hogy az IP-címtartományok, illetve országok/régiók számára csak az adott logikai csoportokban engedélyezze a hozzáférést.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: Azure Active Directory használata központi hitelesítési és engedélyezési rendszerként, valamint a Azure batch alkalmazások hitelesítésének integrálása az Azure ad-vel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

- [Batch-alkalmazások hitelesítése az Azure AD-vel](batch-aad-auth.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity hozzáférési felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférését rendszeresen áttekintheti, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: diagnosztikai beállítások létrehozása Azure Active Directory felhasználói fiókok számára, a naplók és a bejelentkezési naplók elküldése egy Azure log Analytics-munkaterületre. A kívánt riasztások konfigurálása az Azure Log Analytics-munkaterületen belül.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory kockázati észlelésekkel és az Identity Protection szolgáltatással konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Emellett további vizsgálatot is betöltheti az Azure Sentinelbe.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során  

**Útmutató**: nem érhető el; Azure Batch Ügyfélszéf még nem támogatott.
 
- [Ügyfélszéf támogatott szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Azure Batch készleteket virtuális hálózattal/alhálózattal kell elválasztani, megfelelően címkézve, és hálózati biztonsági csoportokkal (NSG) kell biztosítani. Azure Batch az adataikat egy biztonságos Azure Storage-fiókban kell tárolni.

- [Azure Batch-készlet létrehozása egy Virtual Networkon belül](batch-virtual-network.md)

- [Azure Storage-fiókok biztonságossá tétele](../storage/blobs/security-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: az olyan Azure Storage-fiókok esetében, amelyek bizalmas információkat tartalmaznak a Azure batch készlet (ek) hez, és amelyek bizalmas adatokat tartalmaznak, megjelölhetik őket a címkék használatával, és biztonságossá tehetik azokat az Azure ajánlott eljárásaival.

Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

- [Azure Storage-fiókok biztonságossá tétele](../storage/blobs/security-recommendations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. A Microsoft Azure-erőforrások alapértelmezés szerint a TLS 1,2-et fogják egyeztetni. Győződjön meg arról, hogy a Azure Batch készletekhez vagy adattárakhoz (Azure Storage-fiókokhoz) csatlakozó ügyfelek képesek a TLS 1,2-es vagy újabb egyeztetésére.

Győződjön meg arról, hogy HTTPS szükséges a Azure Batch adatait tartalmazó Storage-fiók eléréséhez.

- [Az Azure Storage-fiók titkosításának megismerése az átvitel során](../storage/blobs/security-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az olyan Azure Storage-fiókok esetében, amelyek bizalmas információkat tartalmaznak a Azure batch készlet (ek) hez, és amelyek bizalmas adatokat tartalmaznak, megjelölhetik őket a címkék használatával, és biztonságossá tehetik azokat az Azure ajánlott eljárásaival.

Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

- [Azure Storage-fiókok biztonságossá tétele](../storage/blobs/security-recommendations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozhatja az Azure-erőforrások felügyeleti síkjával, például a Batch-fiókkal, a Batch-készlettel és a Storage-fiókokkal való hozzáférést.

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Azure batch fiókjához társított Storage-fiókok esetében ajánlott engedélyezni a Microsoft számára a titkosítási kulcsok kezelését, azonban szükség esetén lehetősége van a saját kulcsok kezelésére.

Az Azure Disk Encryption használatával a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítése érdekében biztosíthatja az adatai védelmét és védelmét. Az összes felügyelt lemez, pillanatkép, kép és a meglévő lemezekre írt adatok automatikusan titkosítva vannak a platform által felügyelt kulcsokkal.

- [Az Azure Storage-fiókok titkosítási kulcsainak kezelése](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Lemezes titkosítást használó készlet létrehozása](disk-encryption.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure batch-fiókokhoz/készletekhez kapcsolódó vagy azokhoz kapcsolódó kritikus Azure-erőforrásokra vonatkoznak.

A Azure Batch készlethez társított Storage-fiókok diagnosztikai beállításainak konfigurálása a készlet adatain végzett összes szifilisz-művelet figyeléséhez és naplózásához.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

- [További naplózás/naplózás engedélyezése egy Azure Storage-fiókhoz](../storage/common/manage-storage-analytics-logs.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure batch Pool-csomópontok esetében a biztonsági rések kezelésére szolgáló megoldás kezeléséért felelős.

Ha Rapid7, Qualys vagy bármely más sebezhetőség-kezelési platformra vonatkozó előfizetéssel rendelkezik, manuálisan telepítheti a sebezhetőségi felmérési ügynököket a Batch-készlet csomópontjain, és kezelheti a csomópontokat a megfelelő portálon keresztül.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: a Microsoft az alapszintű Azure batch Pool-csomópontok rendszerképeinek karbantartásához és frissítéséhez. Győződjön meg arról, hogy Azure Batch a készlet csomópontjainak operációs rendszere továbbra is a fürt élettartama alatt marad, ami szükségessé teheti az automatikus frissítések engedélyezését, a csomópontok figyelését vagy rendszeres újraindítást.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: Azure batch Pool-csomópontok harmadik féltől származó alkalmazásai a fürt élettartamának időtartamára kiterjedően maradnak, ami szükségessé teheti az automatikus frissítések engedélyezését, a csomópontok figyelését vagy az időszakos újraindítást.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: Ha Rapid7, Qualys vagy bármely más sebezhetőségi kezelési platformra vonatkozó előfizetéssel rendelkezik, használhatja a gyártó portálját a biztonsági rések vizsgálatának megtekintésére és összehasonlítására.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: általános kockázati pontozási programot (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket használjon.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetése (i) n belüli összes erőforrást (például a számítási, tárolási, hálózati stb.). Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Bár a klasszikus Azure-erőforrások az Azure Resource Graph Explorerben is felderíthető, erősen ajánlott Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a jóváhagyott Azure-erőforrások és jóváhagyott szoftverek listájának meghatározása számítási erőforrásokhoz

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: Azure batch Pool-csomópontok esetében hozzon létre egy külső gyártótól származó megoldást, amely figyeli a nem jóváhagyott szoftveralkalmazások fürt csomópontjait.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: Azure batch Pool-csomópontok esetében hozzon létre egy külső gyártótól származó megoldást, amely figyeli a nem jóváhagyott szoftveralkalmazások fürt csomópontjait.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: Azure batch Pool-csomópontok esetében hozzon létre egy harmadik féltől származó megoldást, amely megakadályozza a jogosulatlan szoftverek futtatását.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával: 
- Nem engedélyezett erőforrástípusok 
- Engedélyezett erőforrástípusok 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: Azure batch Pool-csomópontokhoz hozzon létre egy harmadik féltől származó megoldást, amely megakadályozza a jogosulatlan fájltípusok végrehajtását.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft.BatCH" névtérben egyéni szabályzatok létrehozásához a Azure batch-fiókok és-készletek konfigurációjának naplózásához vagy érvényesítéséhez.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: biztonságos konfigurációk létrehozása a Batch Pool-csomópontok operációs rendszeréhez.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: a Batch-fiókhoz és-készletekhez (például virtuális hálózatokhoz, alhálózatokhoz, Azure-tűzfalakhoz, Azure Storage-fiókokhoz stb.) kapcsolódó Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés, ha nem létezik] lehetőséget. A következő névterekben található Azure Policy aliasokat használhatja egyéni házirendek létrehozásához:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: Azure Batch a Microsoft által felügyelt és karbantartott készlet operációs rendszer lemezképeit. Az operációs rendszer szintű állapot konfigurációjának végrehajtásáért felelős.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha a Azure batch-fiókokhoz,-készletekhez vagy kapcsolódó erőforrásokhoz egyéni Azure Policy definíciókat használ, az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: Ha egyéni rendszerképeket használ a Azure batch készletekhez, használja a szerepköralapú hozzáférés-vezérlést (RBAC) annak biztosítására, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez.

- [A RBAC megismerése az Azure-ban](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Az RBAC konfigurálása az Azure-ban](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: beépített Azure Policy-definíciók használata a Azure batch kapcsolódó erőforrás-konfigurációk riasztásához, naplózásához és érvényesítéséhez.  Használjon Azure Policy aliasokat a "Microsoft.BatCH" névtérben egyéni szabályzatok létrehozásához a Azure Batch-fiókokhoz és-készletekhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: egy külső gyártótól származó megoldás implementálása a Azure batch Pool-csomópontok operációs rendszereinek kívánt állapotának fenntartásához.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft.BatCH" névtérben egyéni szabályzatok létrehozásához az Azure batch-példány konfigurációjának naplózásához vagy érvényesítéséhez. A kifejezetten a Azure Batchhoz létrehozott beépített szabályzatokat vagy a Azure Batch által használt erőforrásokat is használhatja, például:
- Az alhálózatokat hálózati biztonsági csoporttal kell társítani – a Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk
- A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: külső gyártótól származó megoldás implementálása Azure batch Pool-csomópontok operációs rendszerei állapotának figyeléséhez.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure Storage-fiókokon belüli készlet-tárolás kulcsainak kezeléséhez Azure Key Vault használható Azure batch üzemelő példányok esetében.

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Azure Key Vault létrehozása](../key-vault/general/quick-create-portal.md)

- [Hitelesítés Key Vault](../key-vault/general/authentication.md)
- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: a Windows Defender használata a Windows operációs rendszerek esetében az egyes Azure batch Pool-csomópontokon, illetve a Linux használata esetén saját kártevő szoftvereket is biztosíthat.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például Azure batch), azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage stb. A Microsoft nem fér hozzá az ügyféladatokat ezekben a példányokban.

- [Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: a Windows Defender használata az egyes Azure batch Pool-csomópontokon Windows operációs rendszerek esetén, valamint az automatikus frissítés engedélyezése. A Linux használata esetén saját kártevő-ellenes megoldást biztosíthat.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: Ha Azure Storage-fiókot használ a Azure batch készlet adattárához, válassza ki a megfelelő redundancia-beállítást (LRS, ZRS, GRS, ra-GRS). 

- [Tárolási redundancia konfigurálása Azure Storage-fiókokhoz](../storage/common/storage-redundancy.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: Ha Azure Storage-fiókot használ a Azure batch készlet adattárához, válassza ki a megfelelő redundancia-beállítást (LRS, ZRS, GRS, ra-GRS). Ha a Azure Batch üzembe helyezésének bármely részéhez Azure Key Vault használ, győződjön meg róla, hogy a kulcsok biztonsági mentése megtörténik.

- [Tárolási redundancia konfigurálása Azure Storage-fiókokhoz](../storage/common/storage-redundancy.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Ha saját kulcsokat felügyel az Azure Storage-fiókokhoz vagy az Azure batch-implementációhoz kapcsolódó bármely más erőforráshoz, a biztonsági másolatban szereplő kulcsok visszaállítását rendszeresen tesztelje.

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [Ügyfél által felügyelt kulcs visszaállítása a PowerShell-lel](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Ha Azure Key Vaultt használ a Azure batch Pool Storage-fiókokhoz kapcsolódó kulcsok tárolására, a kulcsok véletlen vagy rosszindulatú Törlés elleni megvédéséhez engedélyezze a Azure Key Vault Soft-Delete.

- [A Soft delete engedélyezése Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Ellenőrizze, hogy vannak-e olyan írásos incidensek, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének és kezelésének fázisait.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensekkel kapcsolatos kapcsolattartási adatokat arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az Ön adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: – [kérjük, kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

A Microsoft által felügyelt felhő-infrastruktúrával,-szolgáltatásokkal és-alkalmazásokkal kapcsolatos, a Microsoft által felügyelt felhőalapú infrastruktúra, szolgáltatások és alkalmazások tekintetében a Microsoft stratégiájának és végrehajtásának további információit találhatja: 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)