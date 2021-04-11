---
title: Event Hubs Azure biztonsági alapterve
description: A Event Hubs biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 57862a450d313f5d8850b14047ecc3d25d6ba5c1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563684"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Event Hubs Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Event Hubsre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Event Hubs vonatkozó kapcsolódó útmutatás. A Event Hubs nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Event Hubs teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Event Hubs biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: az Azure Event Hubs és a virtuális hálózati szolgáltatás-végpontok integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos elérését olyan munkaterhelések esetén, mint például a virtuális hálózatokhoz kötött virtuális gépek, és a hálózati forgalom elérési útja mindkét végén védett.

Ha legalább egy virtuális hálózati alhálózat szolgáltatási végponthoz kötve van, a megfelelő Event Hubs névtér többé nem fogadja el a forgalmat bárhonnan, de a virtuális hálózatokban engedélyezett alhálózatokat. A virtuális hálózat szemszögéből a Event Hubs névteret egy szolgáltatás-végponthoz kötve konfigurálja egy elkülönített hálózati alagutat a virtuális hálózat alhálózatáról az üzenetküldési szolgáltatásba. 

Létrehozhat egy privát végpontot is, amely egy olyan hálózati adapter, amely az Azure Private link Service használatával a szolgáltatáshoz kapcsolódóan és biztonságosan csatlakoztatja Event Hubs szolgáltatást. A privát végpont egy magánhálózati IP-címet használ a virtuális hálózatról, amely hatékonyan hozza a szolgáltatást a virtuális hálózatba. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. 

Az Azure Event Hubs névteret tűzfalak használatával is biztonságossá teheti. A Event Hubs támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. A tűzfalszabályok a Azure Portal, Azure Resource Manager sablonok vagy az Azure CLI vagy a Azure PowerShell használatával állíthatók be.

- [Virtuális hálózati szolgáltatásbeli végpontok használata az Azure Event Hubs](event-hubs-service-endpoints.md)

- [Az Azure Event Hubs integrálása az Azure Private-hivatkozással](private-link-service.md)

- [Az IP-tűzfalszabályok konfigurálása Azure Event Hubs-névterekhez](event-hubs-ip-filtering.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózati védelmi javaslatok követése a Event Hubs-erőforrások biztonságossá tételéhez az Azure-ban. Ha Azure-beli virtuális gépeket használ az esemény-hubok eléréséhez, engedélyezze a hálózati biztonsági csoport folyamatábráit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: engedélyezze az DDoS Protection standardot az esemény-hubokhoz társított virtuális hálózatokon az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelem érdekében. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [További információ az Azure Security Center integrált veszélyforrások felderítéséről](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Ha Azure-beli virtuális gépeket használ az esemény-hubok eléréséhez, engedélyezze a hálózati biztonsági csoport folyamatábráit, és küldje el a naplókat a forgalmi naplózáshoz a Storage-fiókba. Emellett a hálózati biztonsági csoport folyamatábráit is elküldheti egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze Network Watcher csomagok rögzítését.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: Ha Azure-beli virtuális gépeket használ az Event hubok eléréséhez, válasszon ki egy ajánlatot az Azure piactéren, amely támogatja az azonosítók/IP-címek funkciót a hasznos adatok vizsgálatára szolgáló funkciókkal. Ha a szervezete nem igényli a behatolás észlelését és/vagy megelőzését a hasznos adatok ellenőrzése alapján, akkor az Azure Event Hubs beépített tűzfal funkcióját használhatja. Az IP-címek korlátozott tartományához vagy a tűzfalszabályok használatával korlátozhatja a hozzáférést a Event Hubs-névtérhez.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Tűzfalszabály hozzáadása a Event Hubs egy adott IP-cím esetében](event-hubs-ip-filtering.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure Event Hubs névterekhez társított hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a **Microsoft. EventHub** és a **Microsoft. Network** névterekben, hogy egyéni szabályzat-definíciókat hozzon létre a Event Hubs névterek hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az Azure Event Hubshoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet, például:

- Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia.

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure beépített szabályzat Event Hubs névtérhez](../governance/policy/samples/built-in-policies.md#event-hub)

- [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a virtuális hálózatok és az Event hub-hoz társított hálózati biztonsággal és adatforgalommal kapcsolatos egyéb erőforrások címkéit használhatja.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és az Azure Event Hubs-hoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a Azure monitoron belül konfigurálja az Event hub-hoz kapcsolódó naplókat a tevékenység naplójában és az Event hub diagnosztikai beállításaiban, hogy a naplókat egy log Analytics munkaterületre küldje, vagy a hosszú távú archiválási tárterületre.

- [Az Azure Event Hubs diagnosztikai beállításainak konfigurálása](event-hubs-diagnostic-logs.md)

- [Az Azure-tevékenység naplójának ismertetése](../azure-monitor/essentials/platform-logs-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure Event Hubs-névtér diagnosztikai beállításainak engedélyezése. Az Azure Event Hubs diagnosztikai beállításai három kategóriába sorolhatók: archiválási naplók, operatív naplók és az autoscale naplók. Az operatív naplók lehetővé teszik a Event Hubs műveletek során végrehajtott információk rögzítését, pontosabban a művelet típusát, beleértve az Event hub létrehozását, a felhasznált erőforrásokat és a művelet állapotát.

Emellett engedélyezheti az Azure-műveletnapló diagnosztikai beállításait, és elküldheti azokat egy Azure Storage-fiókba, az Event hub-ba vagy egy Log Analytics munkaterületre. A Tevékenységnaplók betekintést nyújtanak az Azure-Event Hubs és más erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók használatával meghatározhatja az Azure Event Hubs-névterekben végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mit, ki és mikor" lehetőséget.

- [Az Azure Event Hubs diagnosztikai beállításainak engedélyezése](event-hubs-diagnostic-logs.md)

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi szabályainak megfelelően, hogy rögzítse és áttekintse az Event hub-hoz kapcsolódó incidenseket.

- [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: elemezze és figyelje a rendellenes viselkedésre vonatkozó naplókat, és rendszeresen tekintse át az Event hub-hoz kapcsolódó eredményeket. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor Log Analytics. Azt is megteheti, hogy az Azure Sentinel vagy egy külső gyártótól származó Rendszerinformáció-és eseménykezelő megoldás számára engedélyezheti és elvégezheti a fedélzeti adatokat.

- [További információ a Log Analytics munkaterületről](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Azure monitoron belül konfigurálja az Azure Event Hubshoz kapcsolódó naplókat a tevékenység naplójában, és Event Hubs diagnosztikai beállítások a naplók log Analytics munkaterületre való küldéséhez, illetve a hosszú távú archiválási tároláshoz. Log Analytics munkaterület használatával riasztásokat hozhat létre a biztonsági naplókban és eseményekben található rendellenes tevékenységekhez.

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban. 

- [Az Azure-tevékenység naplójának ismertetése](../azure-monitor/essentials/platform-logs-overview.md)

- [Az Azure Event Hubs diagnosztikai beállításainak konfigurálása](event-hubs-diagnostic-logs.md)

- [Riasztás Log Analytics-munkaterület naplófájljainak adatvédelme](../azure-monitor/alerts/tutorial-response.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Event Hubshoz való hozzáférés szabályozása Azure Active Directory (Azure ad) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az adatsík Event Hubshoz való hozzáférését felügyelt identitások vagy Alkalmazásregisztrációk, valamint közös hozzáférési aláírások segítségével szabályozhatja az Azure AD-n keresztül. A közös hozzáférésű aláírásokat az adott esemény-hubhoz csatlakozó ügyfelek használják, és bármikor újra létrehozhatók.

- [A Event Hubs közös hozzáférési aláírásainak ismertetése](authenticate-shared-access-signature.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: a Microsoft Azure Azure Active Directory (Azure ad) alapján integrált hozzáférés-vezérlési felügyeletet biztosít az erőforrásokhoz és alkalmazásokhoz. Az Azure AD és az Azure Event Hubs használatának egyik legfőbb előnye, hogy a hitelesítő adatait többé nem kell a kódban tárolnia. Ehelyett OAuth 2,0 hozzáférési tokent igényelhet a Microsoft Identity platformon. A tokent kérő erőforrás neve https: \/ /eventhubs.Azure.net/. Az Azure AD az alkalmazást futtató rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatás) hitelesítését végzi. Ha a hitelesítés sikeres, az Azure AD egy hozzáférési jogkivonatot ad vissza az alkalmazásnak, és az alkalmazás a hozzáférési token használatával engedélyezheti az Azure Event Hubs-erőforrásokra vonatkozó kéréseket.

- [Alkalmazások hitelesítése az Azure AD-vel Event Hubs erőforrások eléréséhez](authenticate-application.md)

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze az Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait az Event hub-kompatibilis erőforrások védelméhez.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: a rendszerjogosultságú hozzáférésű munkaállomások (Paw) használata többtényezős hitelesítéssel, amely az Event hub-kompatibilis erőforrások bejelentkezésére és konfigurálására van konfigurálva.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](/security/compass/privileged-access-devices)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a naplók és riasztások generálásához Azure Active Directory (Azure AD) Privileged Identity Management használata, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg. További naplózáshoz küldje el Azure Security Center kockázatkezelési riasztásokat a Azure Monitorba, és konfigurálja az egyéni riasztásokat/értesítéseket a műveleti csoportok használatával.

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

- [Műveleti csoportok konfigurálása egyéni riasztásokhoz és értesítésekhez](../azure-monitor/alerts/action-groups.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként az Azure-erőforrások, például a Event Hubs számára. Ez lehetővé teszi, hogy az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) a rendszergazdai bizalmas erőforrások számára legyen elérhető.

- [ Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz az Azure AD használatával](authorize-access-azure-active-directory.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

Emellett rendszeresen elforgatja Event Hubs megosztott hozzáférési aláírásait.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [A Event Hubs közös hozzáférési aláírásainak ismertetése](authenticate-shared-access-signature.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik a harmadik féltől származó rendszerinformációk és rendezvényszervezés megoldás vagy egy figyelési eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics belül is konfigurálhatja a kívánt naplózási riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz az Azure AD használatával](authorize-access-azure-active-directory.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) identitás-és kockázati észlelési funkcióinak használatával konfigurálhatja az Event Hubs-kompatibilis erőforrásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához engedélyeznie kell az automatikus válaszokat az Azure Sentinel használatával.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: jelenleg nem érhető el; Event Hubs esetében Ügyfélszéf még nem támogatott.

- [Ügyfélszéf által támogatott szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: címkék használata a Event Hubshoz kapcsolódó erőforrásokon a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követésében.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések implementálása, választhatóan fejlesztési, tesztelési és éles felügyeleti csoportokkal. Event Hubs névtereket a virtuális hálózatnak el kell választania, ahol a szolgáltatási végpontok engedélyezve vannak és címkézve megfelelően vannak megjelölve.

Az Azure Event Hubs-névteret tűzfalak használatával is biztonságossá teheti. Az Azure Event Hubs támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. A tűzfalszabályok a Azure Portal, Azure Resource Manager sablonok vagy az Azure CLI vagy a Azure PowerShell használatával állíthatók be.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Az IP-tűzfalszabályok konfigurálása az Azure Event Hubs-névterekhez](event-hubs-ip-filtering.md)

- [Címkék létrehozása és felhasználása](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: Ha virtuális gépeket használ az esemény-hubok eléréséhez, használja a virtuális hálózatokat, a szolgáltatási végpontokat, a Event Hubs tűzfalat, a hálózati biztonsági csoportokat és a szolgáltatási címkéket az adatkiszűrése lehetőségének csökkentése érdekében.

A Microsoft felügyeli az Azure Event Hubs mögöttes infrastruktúráját, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az IP-tűzfalszabályok konfigurálása az Azure Event Hubs-névterekhez](event-hubs-ip-filtering.md)

- [Virtual Network szolgáltatási végpontok megismerése az Azure-Event Hubs](event-hubs-service-endpoints.md)

- [Az Azure Event Hubs integrálása az Azure Private-hivatkozással](private-link-service.md)

- [A hálózati biztonsági csoportok és a szolgáltatási címkék ismertetése](../virtual-network/network-security-groups-overview.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Event Hubs számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure Event Hubs támogatja a Azure Active Directory (Azure ad) használatát az Event Hubs-erőforrások kéréseinek engedélyezéséhez. Az Azure AD-vel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével engedélyeket biztosíthat egy rendszerbiztonsági tag számára, amely lehet egy felhasználó vagy egy egyszerű alkalmazás.

- [Ismerje meg az Azure-RBAC és az Azure-Event Hubs elérhető szerepköröket](authorize-access-azure-active-directory.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az Azure Event Hubs támogatja az inaktív adatok titkosítását a Microsoft által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsokkal. Ez a funkció lehetővé teszi az Azure Event Hubs-adatok inaktív állapotban való titkosításához használt ügyfél által felügyelt kulcsok elérésének létrehozását, elforgatását, letiltását és visszavonását.

- [Ügyfél által felügyelt kulcsok konfigurálása az Azure-Event Hubs titkosításához](configure-customer-managed-key.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel az Azure Event Hubs és más kritikus vagy kapcsolódó erőforrások éles példányain végezheti el a módosításokat.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetéseken belüli összes erőforrást (beleértve az Azure Event Hubs névtereit is). Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: címkézés, felügyeleti csoportok és külön előfizetések használata, ahol szükséges, az Azure Event Hubs névterek és a kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetéseken belüli erőforrásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

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

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása az Azure Event Hubs üzemelő példányokhoz. Használjon Azure Policy aliasokat a **Microsoft. EventHub** névtérben, hogy egyéni szabályzatokat hozzon létre a konfigurációk naplózásához és érvényesítéséhez. Az Azure Event Hubs beépített szabályzat-definícióit is használhatja, például:

- Az Event hub diagnosztikai naplóit engedélyezni kell

- Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [Azure beépített szabályzat Event Hubs névtérhez](../governance/policy/samples/built-in-policies.md#event-hub)

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: a Azure Policy [megtagadás] és a [telepítés, ha nem létezik] hatással van a Event Hubs-kompatibilis erőforrások biztonságos beállításainak betartatására. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

 
- [További információ a Azure Policy hatásáról](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. EventHub" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. EventHub" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Az Azure Event Hubs-környezetek és a kapcsolódó erőforrások konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés, ha nem léteznek] effektusokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure-beli virtuális gépek vagy a Azure app Serviceon futó webalkalmazások használata az Event hub-eszközök elérésére használatos, Managed Service Identity az Azure Key Vaultekkel együtt egyszerűsítheti és biztonságossá teheti az Azure-beli Event Hubs üzemelő példányok közös hozzáférésű aláírás-kezelését. Győződjön meg arról, Key Vault konfigurálva van a helyreállítható törlés engedélyezve beállítással.

- [Felügyelt identitás hitelesítése Azure Active Directory (Azure AD) használatával Event Hubs erőforrások eléréséhez](./authenticate-managed-identity.md?tabs=latest)

- [Ügyfél által felügyelt kulcsok konfigurálása Event Hubshoz](configure-customer-managed-key.md)

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault létrehozása](../key-vault/secrets/quick-create-portal.md)

- [Key Vault hitelesítés biztosítása felügyelt identitással](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az azure Event Hubs Azure Key Vault-beli virtuális gépekhez vagy Azure app Service futó webalkalmazásokhoz Managed Service Identity, amelyek az Event hubok elérésére használatosak Győződjön meg arról, Key Vault konfigurálva van a helyreállítható törlés engedélyezve beállítással.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (Azure AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitás hitelesítése az Azure AD-vel Event Hubs erőforrások eléréséhez](./authenticate-managed-identity.md?tabs=latest)

- [Ügyfél által felügyelt kulcsok konfigurálása Event Hubshoz](configure-customer-managed-key.md)

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

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

**Útmutató**: a nem számítási Azure-erőforrásokra feltöltött tartalmak előzetes vizsgálata, például Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure cache for Redis esetében), de nem az ügyfél tartalmán fut.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: az Azure Event Hubs geo-vész-helyreállításának konfigurálása. Ha a teljes Azure-régiók vagy-adatközpontok (ha nincsenek használatban rendelkezésre állási zónák) a tapasztalatok leállását tapasztalják, kritikus fontosságú, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjön. Így a Geo-vész-helyreállítás és a Geo-replikáció minden vállalat számára fontos funkció. Az Azure Event Hubs a Geo-vész-helyreállítást és a Geo-replikációt is támogatja a névtér szintjén. 

- [Az Azure-Event Hubs földrajzi katasztrófa-helyreállításának megismerése](./event-hubs-geo-dr.md#availability-zones)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: az Azure Event Hubs az Azure Storage Service encryption (Azure SSE) használatával biztosítja az inaktív adatok titkosítását. Event Hubs az Azure Storage-ra támaszkodik az adattárolásra, és alapértelmezés szerint az Azure Storage-ban tárolt összes adattal titkosították a Microsoft által felügyelt kulcsokkal. Ha az ügyfél által felügyelt kulcsok tárolására Azure Key Vault használ, ügyeljen a kulcsok rendszeres automatikus biztonsági mentésére.

Gondoskodjon arról, hogy a Key Vault titkos kódok rendszeres automatikus biztonsági mentése a következő PowerShell-paranccsal történjen: Backup-AzKeyVaultSecret

- [Ügyfél által felügyelt kulcsok konfigurálása az Azure-Event Hubs inaktív adatok titkosításához](configure-customer-managed-key.md)

- [Key Vault titkos kódok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az ügyfél által felügyelt kulcsok biztonsági másolatának visszaállítása.

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a nem kötelező Törlés engedélyezése a Key Vault a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához. Az Azure Event Hubs megköveteli, hogy az ügyfél által felügyelt kulcsok helyreállítva legyenek, és ne legyenek beállítva a törlés.

Állítsa be az Event Hubs-adatrögzítéshez használt Azure Storage-fiókhoz tartozó helyreállítható törlést. Vegye figyelembe, hogy ez a funkció még nem támogatott Azure Data Lake Storage Gen 2 esetében.

- [A Soft delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Key Vault beállítása kulcsokkal](configure-customer-managed-key.md)

- [Az Azure Storage-blobok helyreállítható törlése](//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Ellenőrizze, hogy vannak-e olyan írásos incidensek, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének és kezelésének fázisait.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után. 

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

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

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
