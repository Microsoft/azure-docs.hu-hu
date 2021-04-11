---
title: Service Bus Azure biztonsági alapterve
description: A Service Bus biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: service-bus-messaging
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9f437c01b4c8ec1df618ac3f4be9ecd55a930fb1
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967794"
---
# <a name="azure-security-baseline-for-service-bus"></a>Service Bus Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Service Busre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Service Bus vonatkozó kapcsolódó útmutatás. Az Service Busre nem alkalmazható **vezérlők** , vagy amelyek esetében a felelősség a Microsoft által lett kizárva.

Ha szeretné megtekinteni, hogyan Service Bus teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Service Bus biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: a Service Bus és az Azure Private link szolgáltatás integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos privát elérését olyan munkaterhelések esetén, mint a virtuális hálózatokhoz kötött virtuális gépek. Hozzon létre egy privát végponti kapcsolódást a Service Bus névtérhez. A privát végpont egy magánhálózati IP-címet használ a virtuális hálózatról, amely hatékonyan hozza a szolgáltatást a virtuális hálózatba. A szolgáltatás felé irányuló összes forgalom átirányítható az adott magánhálózati végponton keresztül, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre.

A Azure Service Bus névteret tűzfalak használatával is biztonságossá teheti. A Azure Service Bus támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. A tűzfalszabályok a Azure Portal, Azure Resource Manager sablonok vagy az Azure CLI vagy a Azure PowerShell használatával állíthatók be.

- [Azure Service Bus névtér elérésének engedélyezése privát végpontokon keresztül](private-link-service.md)

- [Azure Service Bus névtér elérésének engedélyezése adott IP-címekről vagy tartományokból](service-bus-ip-filtering.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. ServiceBus**:

[!INCLUDE [Resource Policy for Microsoft.ServiceBus 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.servicebus-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: Ha Azure-beli virtuális gépeket használ a Service Bus entitásokhoz való hozzáféréshez, engedélyezze a hálózati biztonsági csoport (NSG) folyamatábráit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását. 

A Azure Security Center használatával és a hálózati védelmi javaslatok követésével biztonságossá teheti Service Bus erőforrásait az Azure-ban.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Service Bus névterekhez társított virtuális hálózatok DDoS Protection szabványának engedélyezése az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelemhez. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center integrált veszélyforrások felderítése](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Ha Azure-beli virtuális gépeket használ a Service Bus entitásokhoz való hozzáféréshez, Network Watcher a csomagok rögzítése segítségével megvizsgálhatja a rendellenes tevékenységeket.

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: Ha Azure-beli virtuális gépeket használ a Service Bus entitásokhoz való hozzáféréshez, válasszon ki egy ajánlatot az Azure piactéren, amely támogatja az azonosítók/IP-címek funkciót a hasznos adatok vizsgálatával. Ha a szervezete nem igényli a behatolás észlelését és/vagy megelőzését a hasznos adatok ellenőrzése alapján, akkor Azure Service Bus beépített tűzfal-funkcióját használhatja. Az IP-címek korlátozott tartományához vagy a tűzfalszabályok használatával korlátozhatja a hozzáférést a Service Bus-névtérhez.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Tűzfalszabály hozzáadása egy adott IP-cím Service Bus névterekben](service-bus-ip-filtering.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Virtual Network szolgáltatás-címkék használatával meghatározhatja a hálózati biztonsági csoportokon vagy az Azure-tűzfalakon elérhető hálózati hozzáférés-vezérléseket, amelyek Service Bus erőforrásokra irányuló és onnan érkező forgalmat szűrik. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ServiceBus), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. 

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Azure Service Bus névterekhez társított hálózati erőforrásokra vonatkozó szabványos biztonsági konfigurációk definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. ServiceBus" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Service Bus névterek hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a Azure Service Bushoz kapcsolódó beépített szabályzat-definíciókat is, például a következőket:

- Service Bus virtuális hálózati szolgáltatás végpontját kell használnia
- A Service Bus lévő diagnosztikai naplókat engedélyezni kell

Egyéni szabályzat-definíciókat is létrehozhat, ha a beépített definíciók nem felelnek meg a szervezet igényeinek.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure beépített szabályzat Service Bus névtérhez](https://docs.microsoft.com/azure/service-bus-messaging/policy-reference#azure-service-bus-messaging)

- [Azure Policy minták a hálózatkezeléshez](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a virtuális hálózatokhoz és a hálózati biztonsággal és a Service Bus névterekhez társított forgalomhoz kapcsolódó egyéb erőforrásokhoz használható címkék használata. Az egyes hálózati biztonsági csoportokra vonatkozó szabályok esetében használja a "Leírás" mezőt az üzleti igények, időtartam és egyéb leíró információk megadásához, amelyek engedélyezik a Service Bus névterekhez tartozó hálózatról érkező vagy onnan érkező forgalmat. 

A címkézéshez kapcsolódó beépített Azure Policy-definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról. 

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon. 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md) 

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Service Bus kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása a Azure Monitor segítségével Service Bus erőforrások által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, konfigurálhatja az Azure Storage-fiókokat hosszú távú vagy archiválási tárterületként. Az Azure Sentinelnek vagy egy harmadik féltől származó SIEM-nek küldendő Service Busokhoz kapcsolódó naplókat is konfigurálhat.

- [A Azure Service Bus diagnosztikai beállításainak konfigurálása](service-bus-diagnostic-logs.md)

- [Az Azure-tevékenység naplójának ismertetése](../azure-monitor/essentials/platform-logs-overview.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure Service Bus névtér diagnosztikai beállításainak engedélyezése. Azure Service Bus jelenleg támogatja a tevékenység-és működési vagy diagnosztikai naplókat. A tevékenység naplói információkkal rendelkeznek a feladatokon végzett műveletekről. A diagnosztikai naplók részletes információkat biztosítanak a névtérhez az API használatával végzett műveletekről és műveletekről, illetve a felügyeleti ügyfeleken a Language SDK használatával. Ezek a naplók pontosan rögzítik a művelet típusát, beleértve a várólista létrehozását, a felhasznált erőforrásokat és a művelet állapotát.

- [A Azure Service Bus diagnosztikai beállításainak engedélyezése](service-bus-diagnostic-logs.md)

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. ServiceBus**:

[!INCLUDE [Resource Policy for Microsoft.ServiceBus 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.servicebus-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi szabályainak megfelelően a Service Bus kapcsolatos incidensek rögzítéséhez és felülvizsgálatához.

- [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: elemezze és figyelje a rendellenes viselkedés naplóit, és rendszeresen tekintse át a Service Bus entitásokkal kapcsolatos eredményeket. A Azure Monitor segítségével áttekintheti a naplókat, és lekérdezéseket hajthat végre a Service Bus kapcsolatos naplózási adatokon.

- [További információ a Log Analytics munkaterületről](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Azure Security Center használata log Analytics munkaterülettel a biztonsági naplókban és eseményekben észlelt rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztáshoz. Másik lehetőségként engedélyezheti és elvégezheti az Azure Sentinel szolgáltatásban való ellátást is.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrásokhoz való hozzáférés kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. A Service Bushoz előre definiált beépített szerepkörök tartoznak, ezek a szerepkörök leltározott vagy lekérdezhető olyan eszközökkel, mint például az Azure CLI, a Azure PowerShell vagy a Azure Portal.

- [A Azure Service Bus beépített szerepkörei](https://docs.microsoft.com/azure/service-bus-messaging/authenticate-application#azure-built-in-roles-for-azure-service-bus)

- [Címtárbeli szerepkör beszerzése Azure Active Directoryban (Azure AD) a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Service Bushoz való hozzáférés szabályozása Azure Active Directory (Azure ad) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az adatsík Service Bushoz való hozzáférését felügyelt identitások, Alkalmazásregisztrációk vagy közös hozzáférési aláírások használatával vezérli az Azure AD-n keresztül. A közös hozzáférésű aláírásokat a Service Bus névtérhez csatlakozó ügyfelek használják, és bármikor újra létrehozhatók.

- [A Service Bus közös hozzáférési aláírásainak ismertetése](service-bus-sas.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Egyéni szabályzat-definíciókat is létrehozhat, ha a beépített definíciók nem felelnek meg a szervezet igényeinek.

- [A Azure Security Center használata az identitás és a hozzáférés figyeléséhez](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: a Microsoft Azure Azure Active Directory (Azure ad) alapján integrált hozzáférés-vezérlési felügyeletet biztosít az erőforrásokhoz és alkalmazásokhoz. Az Azure AD és a Azure Service Bus használatának egyik legfőbb előnye, hogy a hitelesítő adatait többé nem kell a kódban tárolnia. Ehelyett OAuth 2,0 hozzáférési tokent igényelhet a Microsoft Identity platformon. A tokent kérő erőforrás neve https://azure.microsoft.com/services/service-bus/ . Az Azure AD az alkalmazást futtató rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatás) hitelesítését végzi. Ha a hitelesítés sikeres, az Azure AD egy hozzáférési jogkivonatot ad vissza az alkalmazásnak, és az alkalmazás a hozzáférési token használatával engedélyezheti Azure Service Bus erőforrásainak kérését.

- [Alkalmazások hitelesítése az Azure AD-vel Service Bus erőforrások eléréséhez](authenticate-application.md)

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze az Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait az Service Bus-kompatibilis erőforrások védelméhez.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: a rendszerjogosultságú hozzáférési munkaállomások (Paw) használata többtényezős hitelesítéssel, amely a Service Bus-kompatibilis erőforrások bejelentkezésére és konfigurálására van konfigurálva.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a Azure Active Directory (Azure ad) biztonsági jelentéseinek és figyelésének használata annak észlelésére, hogy a környezetben gyanús vagy nem biztonságos tevékenységek történnek-e. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: az Azure Active Directory (Azure ad) nevű hely használata, amely lehetővé teszi, hogy csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz férhessenek hozzá.

- [Az Azure AD nevesített helyeinek konfigurálása](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként az Azure-erőforrások, például a Service Bus számára. Ez lehetővé teszi, hogy az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) a rendszergazdai bizalmas erőforrások számára legyen elérhető.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Hozzáférés engedélyezése Service Bus erőforrásokhoz az Azure AD használatával](authenticate-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

A további, rendszeres időközönként elforgatja Service Bus névtér közös hozzáférési aláírását.

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [A Service Bus névtérhez tartozó közös hozzáférési aláírások ismertetése](service-bus-sas.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik az Azure Sentinel vagy egy harmadik féltől származó Siem-eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Ezután a Azure Monitor konfigurálhatja a naplókban előforduló bizonyos műveletekhez szükséges naplózási riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Hozzáférés engedélyezése Service Bus erőforrásokhoz az Azure AD használatával](authenticate-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) identitás-és kockázati észlelési funkcióinak használatával konfigurálhatja az Service Bus-kompatibilis erőforrásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához engedélyeznie kell az automatikus válaszokat az Azure Sentinel használatával.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: jelenleg nem érhető el; Service Bus esetében Ügyfélszéf még nem támogatott.

- [Ügyfélszéf által támogatott szolgáltatások listája](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: címkék használata a Service Bushoz kapcsolódó erőforrásokon a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követésében.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és felügyeleti csoportok implementálása fejlesztési, tesztelési és éles környezetekben. Service Bus névtereket olyan virtuális hálózatokkal kell elválasztani, amelyek saját végpontokkal vannak konfigurálva és címkézve megfelelően.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és felhasználása](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: Ha virtuális gépeket használ a Service Bus entitások eléréséhez, használja a virtuális hálózatokat, a magánhálózati végpontokat, a Service Bus tűzfalat, a hálózati biztonsági csoportokat és a szolgáltatás címkéit, hogy enyhítse az adatkiszűrése lehetőségét.

A Microsoft kezeli a Azure Service Bus alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [IP-tűzfalszabályok konfigurálása Azure Service Bus névterekhez](service-bus-ip-filtering.md)

- [Azure Service Bus névtér elérésének engedélyezése adott virtuális hálózatokból](private-link-service.md)

- [Azure Service Bus névtér elérésének engedélyezése privát végpontokon keresztül](private-link-service.md)

- [A hálózati biztonsági csoportok és a szolgáltatási címkék ismertetése](../virtual-network/network-security-groups-overview.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Azure Service Bus alapértelmezés szerint kikényszeríti a TLS-titkosítású kommunikációt. A TLS 1,0, 1,1 és 1,2 verziók jelenleg támogatottak. A TLS 1,0-es és a 1,1-es verziója azonban olyan elérési úton van, amely az iparágra érvényes, ezért a TLS 1,2-es vagy újabb verzióját használja, ahol lehetséges.

- [A Service Bus biztonsági funkcióinak megismeréséhez lásd: hálózati biztonság](network-security.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Service Bus számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: a Azure Service Bus az Azure Active Directory (Azure ad) használatával támogatja a kérelmek Service Bus entitások számára történő engedélyezését. Az Azure AD-vel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével engedélyeket biztosíthat egy rendszerbiztonsági tag számára, amely lehet egy felhasználó vagy egy egyszerű alkalmazás.

- [Az Azure RBAC és a Azure Service Bus elérhető szerepköreinek megismerése](authenticate-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Azure Service Bus támogatja az inaktív adatok titkosítását a Microsoft által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsokkal. Ez a funkció lehetővé teszi, hogy a Azure Service Bus adatok titkosításához használt ügyfél által felügyelt kulcsokhoz való hozzáférést hozza létre, forgassa el, tiltsa le és vonja vissza.

- [Ügyfél által felügyelt kulcsok konfigurálása a titkosításhoz Azure Service Bus](configure-customer-managed-key.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure Service Bus és más kritikus vagy kapcsolódó erőforrások éles példányain lépnek életbe.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetéseken belüli összes erőforrást (beleértve Azure Service Bus névtereket is). Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ahol szükséges, Azure Service Bus névterek és kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a szervezeti igényeknek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetéseken belüli erőforrásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Egyéni szabályzat-definíciókat is létrehozhat, ha a beépített definíciók nem felelnek meg a szervezet igényeinek.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a Azure Service Bus üzemelő példányokhoz. Emellett a következő beépített szabályzat-definíciókat is használhatja Azure Service Bus például:
- A Service Bus lévő diagnosztikai naplókat engedélyezni kell
- Service Bus virtuális hálózati szolgáltatás végpontját kell használnia a magánhálózatok hálózati forgalmának korlátozására.

Használjon Azure Policy aliasokat a "Microsoft. ServiceBus" névtérben, hogy egyéni szabályzatokat hozzon létre a konfigurációk naplózásához és érvényesítéséhez.

- [Azure-beli beépített szabályzatok a Service Bus ](policy-reference.md)

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: használja a Azure Policy [megtagadás] és a [telepítés, ha nem létezik] lehetőséget a biztonságos beállítások betartatásához a Service Bus-kompatibilis erőforrásokon vagy alkalmazásokon keresztül.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [További információ a Azure Policy hatásáról](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. ServiceBus" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. ServiceBus" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. A Azure Service Bus üzemelő példányok és a kapcsolódó erőforrások konfigurációjának automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [telepítés ha nem létezik] lehetőséget.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure app Service-on futó Azure-beli virtuális gépekhez vagy webalkalmazásokhoz, amelyek a Service Bus-entitások elérésére szolgálnak, a Managed Service Identity a Azure Key Vault együtt használva egyszerűsíthetik és biztonságossá teszik a megosztott hozzáférési aláírások kezelését a Azure Service Bus üzemelő példányok esetében. Győződjön meg arról, Key Vault a helyreállítható törlés engedélyezve van.

- [Felügyelt identitás hitelesítése Azure Active Directory (Azure AD) használatával Service Bus erőforrások eléréséhez](service-bus-managed-service-identity.md)

- [Ügyfél által felügyelt kulcsok konfigurálása Service Bushoz](configure-customer-managed-key.md)

- [Key Vault létrehozása](../key-vault/general/quick-create-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az Azure app Service futó Azure-beli virtuális gépekhez vagy webalkalmazásokhoz, amelyek a Service Bus entitások elérésére szolgálnak, a Managed Service Identity Azure Key Vault együtt, a Azure Service Bus egyszerűsítéséhez és biztonságossá tételéhez használja. Győződjön meg arról, Key Vault a helyreállítható törlés engedélyezve van.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (Azure AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitás hitelesítése az Azure AD-vel Service Bus erőforrások eléréséhez](service-bus-managed-service-identity.md)

- [Ügyfél által felügyelt kulcsok konfigurálása Service Bushoz](configure-customer-managed-key.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a nem számítási Azure-erőforrásokra feltöltött tartalmak előzetes vizsgálata, például Azure Service Bus, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

A Microsoft kártevő-elhárító funkciójának engedélyezése az Azure-szolgáltatásokat támogató mögöttes gazdagépen történik, azonban az ügyfél tartalma nem fut.

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure Service Bus földrajzi katasztrófa-helyreállításának konfigurálása. Ha a teljes Azure-régiók vagy-adatközpontok (ha nincsenek használatban rendelkezésre állási zónák) a tapasztalatok leállását tapasztalják, kritikus fontosságú, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjön. Így a Geo-vész-helyreállítás és a Geo-replikáció minden vállalat számára fontos funkció. A Azure Service Bus a Geo-vész-helyreállítást és a Geo-replikációt is támogatja a névtér szintjén.

- [A Azure Service Bus földrajzi katasztrófák elleni helyreállításának megismerése](service-bus-geo-dr.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure Service Bus az Azure Storage Service encryption (Azure SSE) használatával titkosítja az inaktív adatok titkosítását. Service Bus az Azure Storage-ra támaszkodik az adattárolásra, és alapértelmezés szerint az Azure Storage-ban tárolt összes adattal titkosították a Microsoft által felügyelt kulcsokkal. Ha az ügyfél által felügyelt kulcsok tárolására Azure Key Vault használ, ügyeljen a kulcsok rendszeres automatikus biztonsági mentésére.

Gondoskodjon arról, hogy a Key Vault titkos kódok rendszeres automatikus biztonsági mentése a következő PowerShell-paranccsal történjen: Backup-AzKeyVaultSecret

- [Ügyfél által felügyelt kulcsok konfigurálása Azure Service Bus adatok titkosításához a REST-ben](configure-customer-managed-key.md)

- [Key Vault titkos kódok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az ügyfél által felügyelt kulcsok biztonsági másolatának visszaállítása a Service Bus adatai titkosításához.

- [Ügyfél által felügyelt kulcsok konfigurálása Azure Service Bus adatok titkosításához a REST-ben](configure-customer-managed-key.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a Key Vault a helyreállítható Törlés engedélyezése a kulcsok véletlen vagy rosszindulatú törléssel szembeni megvédéséhez. Azure Service Bus az ügyfél által felügyelt kulcsokat kötelező törölni, és nem kell kiüríteni a konfigurációt.

- [A Soft delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Key Vault beállítása kulcsokkal](../event-hubs/configure-customer-managed-key.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: incidensek kifejlesztése útmutató a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az incidensek vizsgálatát követően. 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [A NIST számítógépes biztonsági incidensek kezelésével kapcsolatos útmutató a saját incidens-válasz tervének létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

Emellett megadhatja a címkéket használó előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt. 

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md) 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [A NIST kiadványa: útmutató az IT-csomagokhoz és-képességekhez tartozó programok teszteléséhez, betanításához és gyakorlatához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. 

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md) 

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a munkafolyamat-automatizálási szolgáltatás használata Azure Security Center a biztonsági riasztásokra és az Azure-erőforrások védelmére vonatkozó ajánlásokra adott válaszok automatikus elindítására. 

- [A Munkafolyamat-automatizálás konfigurálása a Security Centerban](../security-center/workflow-automation.md)

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

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
