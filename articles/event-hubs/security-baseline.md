---
title: Event Hubs Azure biztonsági alapterve
description: Event Hubs Azure biztonsági alapterve
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c5c4aed7a5ee1726d7afb1a671fea1519acfa67c
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806087"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Event Hubs Azure biztonsági alapterve

Az Event Hubs Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: az Event hubok virtuális hálózati szolgáltatásbeli végpontokkal való integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos elérését olyan munkaterhelések esetén, mint például a virtuális hálózatokhoz kötött virtuális gépek, valamint a hálózati forgalom elérési útja mindkét végén.

Ha legalább egy virtuális hálózati alhálózat szolgáltatási végponthoz kötve van, a megfelelő Event Hubs névtér többé nem fogadja el a forgalmat bárhonnan, de a virtuális hálózatokban engedélyezett alhálózatokat. A virtuális hálózat szemszögéből a Event Hubs névteret egy szolgáltatás-végponthoz kötve konfigurálja egy elkülönített hálózati alagutat a virtuális hálózat alhálózatáról az üzenetküldési szolgáltatásba. 

Létrehozhat egy privát végpontot is, amely egy olyan hálózati adapter, amely az Azure Event Hubs szolgáltatáshoz az Azure Private link Service használatával csatlakozik, és biztonságosan csatlakoztatja az Azure szolgáltatást. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. 

Az Azure Event Hubs névteret tűzfalak használatával is biztonságossá teheti. Az Azure Event Hubs támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. A tűzfalszabályok a Azure Portal, Azure Resource Manager sablonok vagy az Azure CLI vagy a Azure PowerShell használatával állíthatók be.

Virtuális hálózati szolgáltatásbeli végpontok használata az Azure Event Hubs használatával: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

További információ: az Azure Event Hubs integrálása az Azure Private-hivatkozással: https://docs.microsoft.com/azure/event-hubs/private-link-service .

Virtuális hálózatok integrációjának és tűzfalának engedélyezése Event Hubs névtérben: https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Az IP-tűzfalszabályok konfigurálása Azure Event Hubs-névterekhez: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózati védelmi javaslatok követése a Event Hubs-erőforrások biztonságossá tételéhez az Azure-ban. Ha Azure-beli virtuális gépeket használ az esemény-hubok eléréséhez, engedélyezze a hálózati biztonsági csoport (NSG) folyamatábráit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében.

A NSG folyamat naplófájljainak engedélyezése: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Azure Security Center által biztosított hálózati biztonság ismertetése: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: engedélyezze az DDoS Protection standardot az esemény-hubokhoz társított virtuális hálózatokon az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelem érdekében. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

A DDoS Protection konfigurálása: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../ddos-protection/manage-ddos-protection.md)

További információ a Azure Security Center integrált veszélyforrások intelligenciáról: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: Ha Azure-beli virtuális gépeket használ az esemény-hubok eléréséhez, engedélyezze a hálózati biztonsági csoport (NSG) folyamatábráit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze Network Watcher csomagok rögzítését.

A NSG folyamat naplófájljainak engedélyezése: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

A Network Watcher engedélyezése: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: Ha Azure-beli virtuális gépeket használ az Event hubok eléréséhez, válasszon ki egy ajánlatot az Azure piactéren, amely támogatja az azonosítók/IP-címek funkciót a hasznos adatok vizsgálatára szolgáló funkciókkal. Ha a szervezete nem igényli a behatolás észlelését és/vagy megelőzését a hasznos adatok ellenőrzése alapján, akkor az Azure Event Hubs beépített tűzfal funkcióját használhatja. Az IP-címek korlátozott tartományához vagy a tűzfalszabályok használatával korlátozhatja a hozzáférést a Event Hubs-névtérhez.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Tűzfalszabály hozzáadása a Event Hubs egy adott IP-cím esetében:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center figyelés**: még nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: nem alkalmazható, ez a javaslat a Azure app Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure Event Hubs névterekhez társított hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. EventHub" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Event Hubs névterek hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az Azure Event Hubshoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet, például:

- Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure beépített szabályzat Event Hubs névtérhez:  https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Azure Policy minták a hálózatkezeléshez: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Azure Blueprint létrehozása: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a virtuális hálózatok és az Event hub-hoz társított hálózati biztonsággal és adatforgalommal kapcsolatos egyéb erőforrások címkéit használhatja.

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és az Azure Event Hubs-hoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

Az Azure Activity log eseményeinek megtekintése és beolvasása: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása a Azure Monitorban: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: nem alkalmazható; A Microsoft fenntartja az Azure-erőforrásokhoz, például az Azure Event Hubshoz használt időforrást a naplók időbélyegzői esetében.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a Azure monitoron belül konfigurálja az Event hub-hoz kapcsolódó naplókat a tevékenység naplójában és az Event hub diagnosztikai beállításaiban, hogy a naplókat egy log Analytics munkaterületre küldje, vagy a hosszú távú archiválási tárterületre.

Az Azure Event Hubs diagnosztikai beállításainak konfigurálása: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Az Azure-tevékenység naplójának ismertetése: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure Event Hubs-névtér diagnosztikai beállításainak engedélyezése. Az Azure Event Hubs diagnosztikai beállításai három kategóriába sorolhatók: archiválási naplók, operatív naplók és az autoscale naplók. Az operatív naplók lehetővé teszik a Event Hubs műveletek során végrehajtott információk rögzítését, pontosabban a művelet típusát, beleértve az Event hub létrehozását, a felhasznált erőforrásokat és a művelet állapotát.

Emellett engedélyezheti az Azure-műveletnapló diagnosztikai beállításait, és elküldheti azokat egy Azure Storage-fiókba, az Event hub-ba vagy egy Log Analytics munkaterületre. A Tevékenységnaplók betekintést nyújtanak az Azure-Event Hubs és más erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók használatával meghatározhatja az Azure Event Hubs-névterekben végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mit, ki és mikor" lehetőséget.

Az Azure Event Hubs diagnosztikai beállításainak engedélyezése: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Az Azure-beli tevékenység naplójának diagnosztikai beállításainak engedélyezése: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi szabályainak megfelelően, hogy rögzítse és áttekintse az Event hub-hoz kapcsolódó incidenseket.

Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: elemezze és figyelje a rendellenes viselkedésre vonatkozó naplókat, és rendszeresen tekintse át az Event hub-hoz kapcsolódó eredményeket. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor Log Analytics. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek.
 

További információ a Log Analytics munkaterületről: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Egyéni lekérdezések végrehajtása a Azure Monitorban: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Az Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: a Azure monitoron belül konfigurálja az Azure Event Hubshoz kapcsolódó naplókat a tevékenység naplójában, és Event Hubs diagnosztikai beállítások a naplók log Analytics munkaterületre való küldéséhez, illetve a hosszú távú archiválási tároláshoz. Log Analytics munkaterület használatával riasztásokat hozhat létre a biztonsági naplókban és eseményekben található rendellenes tevékenységekhez.

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban. 

Az Azure-tevékenység naplójának ismertetése: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Az Azure Event Hubs diagnosztikai beállításainak konfigurálása: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Riasztás Log Analytics-munkaterület naplófájljairól: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Az Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center figyelés**: még nem érhető el

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; Az Event hub nem dolgozza fel a kártevő szoftverek elleni naplózást.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Event Hubs nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ [: Security Control (identitás és Access Control](../security/benchmarks/security-control-identity-access-control.md)).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Active Directory (ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez. 

Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole

Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Event Hubshoz való hozzáférés szabályozása Azure Active Directory (ad) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az adatsík Event Hubshoz való hozzáférését felügyelt identitások vagy Alkalmazásregisztrációk, valamint közös hozzáférési aláírások segítségével szabályozhatja az Azure AD-n keresztül. A közös hozzáférésű aláírásokat az adott esemény-hubhoz csatlakozó ügyfelek használják, és bármikor újra létrehozhatók.

A Event Hubs közös hozzáférési aláírásainak ismertetése: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Az identitás és hozzáférés figyelésének Azure Security Center használata (előzetes verzió): https://docs.microsoft.com/azure/security-center/security-center-identity-access

A Azure Policy használata: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: a Microsoft Azure a Azure Active Directory (ad) alapján integrált hozzáférés-vezérlési felügyeletet biztosít az erőforrásokhoz és alkalmazásokhoz. Az Azure AD és az Azure Event Hubs használatának egyik legfőbb előnye, hogy a hitelesítő adatait többé nem kell a kódban tárolnia. Ehelyett OAuth 2,0 hozzáférési tokent igényelhet a Microsoft Identity platformon. A tokent kérő erőforrás neve https: \/ /eventhubs.Azure.net/. Az Azure AD az alkalmazást futtató rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatás) hitelesítését végzi. Ha a hitelesítés sikeres, az Azure AD egy hozzáférési jogkivonatot ad vissza az alkalmazásnak, és az alkalmazás a hozzáférési token használatával engedélyezheti az Azure Event Hubs-erőforrásokra vonatkozó kéréseket.

Alkalmazások hitelesítése az Azure AD-vel Event Hubs erőforrások eléréséhez: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Az egyszeri bejelentkezés ismertetése az Azure AD-vel: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése az Event hub-kompatibilis erőforrások védelmének elősegítése érdekében.

Az MFA engedélyezése az Azure-ban: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identitás és hozzáférés figyelése Azure Security Centeron belül: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: a rendszerjogosultságú hozzáférési munkaállomások (Paw) használata az Event hub-kompatibilis erőforrások bejelentkezéséhez és konfigurálásához konfigurált multi-Factor Authentication (MFA) használatával.

További tudnivalók az emelt szintű hozzáférésű munkaállomásokról: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Az MFA engedélyezése az Azure-ban: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg. További naplózáshoz küldje el Azure Security Center kockázatkezelési riasztásokat a Azure Monitorba, és konfigurálja az egyéni riasztásokat/értesítéseket a műveleti csoportok használatával.

Privileged Identity Management (PIM) üzembe helyezése: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Az Azure AD kockázati észlelések ismertetése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Műveleti csoportok konfigurálása egyéni riasztásokhoz és értesítésekhez: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.



Elnevezett helyszínek konfigurálása az Azure-ban: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerként az Azure-erőforrások, például a Event Hubs számára. Ez lehetővé teszi, hogy az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) a rendszergazdai bizalmas erőforrások számára legyen elérhető.

 Azure AD-példány létrehozása és konfigurálása: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Ha szeretné megtudni, hogyan integrálható az Azure Event Hubs a Azure Active Directory (HRE) szolgáltatással, tekintse meg a Event Hubs erőforrásokhoz való hozzáférés engedélyezése Azure Active Directory használatával: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

A további, rendszeresen elforgatja Event Hubs megosztott hozzáférési aláírásait.

Az Azure AD jelentéskészítés ismertetése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure Identity hozzáférési felülvizsgálatok használata: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

A Event Hubs közös hozzáférési aláírásainak ismertetése: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: hozzáférhet Azure Active Directory (ad) bejelentkezési tevékenységhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics belül is konfigurálhatja a kívánt naplózási riasztásokat.

Azure-beli tevékenység-naplók integrálása a Azure Monitorba: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Hozzáférés engedélyezése Event Hubs erőforrásokhoz Azure Active Directory használatával: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory Identity Protection és kockázati észlelési funkciói segítségével konfigurálhatja az Event Hubs-kompatibilis erőforrásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához engedélyeznie kell az automatikus válaszokat az Azure Sentinel használatával.

Az Azure AD kockázatos bejelentkezések megtekintése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: jelenleg nem érhető el; Event Hubs esetében Ügyfélszéf még nem támogatott.

Ügyfélszéf által támogatott szolgáltatások listája: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: jelenleg nem érhető el

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: címkék használata a Event Hubshoz kapcsolódó erőforrásokon a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követésében.

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Event Hubs névtereket a virtuális hálózatnak el kell választania, ahol a szolgáltatási végpontok engedélyezve vannak és címkézve megfelelően vannak megjelölve.

Az Azure Event Hubs-névteret tűzfalak használatával is biztonságossá teheti. Az Azure Event Hubs támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. A tűzfalszabályok a Azure Portal, Azure Resource Manager sablonok vagy az Azure CLI vagy a Azure PowerShell használatával állíthatók be.

További Azure-előfizetések létrehozása: https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása: https://docs.microsoft.com/azure/governance/management-groups/create

Az IP-tűzfalszabályok konfigurálása az Azure Event Hubs-névterekhez: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Címkék létrehozása és felhasználása: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Virtual Network létrehozása: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: Ha virtuális gépeket használ az esemény-hubok eléréséhez, használja a virtuális hálózatokat, a szolgáltatási végpontokat, a Event Hubs tűzfalat, a hálózati biztonsági csoportokat és a szolgáltatási címkéket az adatkiszűrése lehetőségének csökkentése érdekében.

A Microsoft felügyeli az Azure Event Hubs mögöttes infrastruktúráját, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

Az IP-tűzfalszabályok konfigurálása az Azure Event Hubs-névterekhez: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Virtual Network szolgáltatási végpontok megismerése az Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Az Azure Event Hubs integrálása az Azure Private-hivatkozással: https://docs.microsoft.com/azure/event-hubs/private-link-service

A hálózati biztonsági csoportok és a szolgáltatási címkék ismertetése: https://docs.microsoft.com/azure/virtual-network/security-overview

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az Azure Event Hubs alapértelmezés szerint kikényszeríti a TLS-titkosítású kommunikációt. A TLS 1,0, 1,1 és 1,2 verziók jelenleg támogatottak. Azonban a TLS 1,0 és a 1,1 egy olyan útvonalon van, amely az iparágra kiterjedő elavult, ezért a TLS 1,2-et használja, ha ez egyáltalán lehetséges.

A Event Hubs biztonsági funkcióinak megismeréséhez tekintse meg a hálózati biztonság:  https://docs.microsoft.com/azure/event-hubs/network-security

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Event Hubs számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure Event Hubs támogatja a Azure Active Directory (ad) használatát a Event Hubs erőforrásokra irányuló kérések engedélyezéséhez. Az Azure AD-vel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével engedélyeket biztosíthat egy rendszerbiztonsági tag számára, amely lehet egy felhasználó vagy egy egyszerű alkalmazás.

Az Azure-RBAC és az Azure Event Hubs elérhető szerepköreinek megismerése: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

A Microsoft kezeli a Event Hubs alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az Azure Event Hubs támogatja az inaktív adatok titkosítását a Microsoft által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsokkal. Ez a funkció lehetővé teszi az Azure Event Hubs-adatok inaktív állapotban való titkosításához használt ügyfél által felügyelt kulcsok elérésének létrehozását, elforgatását, letiltását és visszavonását.

Ügyfél által felügyelt kulcsok konfigurálása az Azure-Event Hubs titkosításához: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel az Azure Event Hubs és más kritikus vagy kapcsolódó erőforrások éles példányain végezheti el a módosításokat.

Riasztások létrehozása az Azure Activity log-eseményekhez: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: nem alkalmazható; A Microsoft a biztonsági rések kezelését a Event Hubs támogató mögöttes rendszereken hajtja végre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; A Microsoft a Event Hubst támogató mögöttes rendszereken végzi a javítások kezelését.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: nem alkalmazható; a teljesítményteszt a számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; A Microsoft a biztonsági rések kezelését a Event Hubs támogató mögöttes rendszereken hajtja végre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: nem alkalmazható; A Microsoft a biztonsági rések kezelését a Event Hubs támogató mögöttes rendszereken hajtja végre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph segítségével lekérdezheti és felderítheti az összes erőforrást (beleértve az Azure Event Hubs névtereit) az előfizetésében. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Lekérdezések létrehozása az Azure Resource Graph használatával: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Az Azure RBAC ismertetése: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: címkézés, felügyeleti csoportok és külön előfizetések használata, ahol szükséges, az Azure Event Hubs névterek és a kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

További Azure-előfizetések létrehozása: https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása: https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph használatával: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása a következővel: Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>A felhasználók Azure Resource Manager interakciójának korlátozása parancsfájlok használatával</div>

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása az Azure Event Hubs üzemelő példányokhoz. Használjon Azure Policy aliasokat a "Microsoft. EventHub" névtérben, hogy egyéni szabályzatokat hozzon létre a konfigurációk naplózásához és érvényesítéséhez. Az Azure Event Hubs beépített szabályzat-definícióit is használhatja, például:

- Az Event hub diagnosztikai naplóit engedélyezni kell

- Az Event hub-nak virtuális hálózati szolgáltatási végpontot kell használnia

Azure beépített szabályzat Event Hubs névtérhez: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Az elérhető Azure Policy aliasok megtekintése: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: használja a Azure Policy [megtagadás] és a [telepítés, ha nem létezik] lehetőséget a biztonságos beállítások betartatásához a Event Hubs-kompatibilis erőforrások között. 

Azure Policy konfigurálása és kezelése:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
További információ a Azure Policy hatásáról:  https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha a Event Hubshoz vagy a kapcsolódó erőforrásokhoz egyéni Azure Policy definíciókat használ, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Kód tárolása az Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow

Az Azure Repos dokumentációja: https://docs.microsoft.com/azure/devops/repos/index

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. EventHub" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: Azure Policy aliasok használata a "Microsoft. EventHub" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Az Azure Event Hubs üzemelő példányok és a kapcsolódó erőforrások konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés, ha nem létezik] lehetőséget.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure-beli virtuális gépek vagy a Azure app Serviceon futó webalkalmazások használata az Event hub-eszközök elérésére használatos, Managed Service Identity az Azure Key Vaultekkel együtt egyszerűsítheti és biztonságossá teheti az Azure-beli Event Hubs üzemelő példányok közös hozzáférésű aláírás-kezelését. Győződjön meg arról, Key Vault a helyreállítható törlés engedélyezve van.

Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Ügyfél által felügyelt kulcsok konfigurálása Event Hubshoz: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Integráció az Azure felügyelt identitásokkal: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

A Key Vault hitelesítése: https://docs.microsoft.com/azure/key-vault/general/authentication

Key Vault hozzáférési szabályzatok kiosztása: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az azure Event Hubs Azure Key Vault-beli virtuális gépekhez vagy Azure app Service futó webalkalmazásokhoz Managed Service Identity, amelyek az Event hubok elérésére használatosak Győződjön meg arról, Key Vault a helyreállítható törlés engedélyezve van.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Ügyfél által felügyelt kulcsok konfigurálása Event Hubshoz: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Felügyelt identitások konfigurálása: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Integráció az Azure felügyelt identitásokkal: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

A hitelesítőadat-olvasó beállítása: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure App Service), azonban az nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a nem számítási Azure-erőforrásokra feltöltött tartalmak előzetes vizsgálata, például Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure cache for Redis esetében), de nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: az Azure Event Hubs geo-vész-helyreállításának konfigurálása. Ha a teljes Azure-régiók vagy-adatközpontok (ha nincsenek használatban rendelkezésre állási zónák) a tapasztalatok leállását tapasztalják, kritikus fontosságú, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjön. Így a Geo-vész-helyreállítás és a Geo-replikáció minden vállalat számára fontos funkció. Az Azure Event Hubs a Geo-vész-helyreállítást és a Geo-replikációt is támogatja a névtér szintjén. 

Az Azure-Event Hubs földrajzi katasztrófák utáni helyreállításának megismerése: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: az Azure Event Hubs az Azure Storage Service encryption (Azure SSE) használatával biztosítja az inaktív adatok titkosítását. Event Hubs az Azure Storage-ra támaszkodik az adattárolásra, és alapértelmezés szerint az Azure Storage-ban tárolt összes adattal titkosították a Microsoft által felügyelt kulcsokkal. Ha az ügyfél által felügyelt kulcsok tárolására Azure Key Vault használ, ügyeljen a kulcsok rendszeres automatikus biztonsági mentésére.

Gondoskodjon arról, hogy a Key Vault titkos kódok rendszeres automatikus biztonsági mentése a következő PowerShell-paranccsal történjen: Backup-AzKeyVaultSecret

Ügyfél által felügyelt kulcsok konfigurálása az Azure Event Hubs-adatok inaktív állapotban történő titkosításához: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Key Vault titkos kódok biztonsági mentése: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a biztonsági másolatok által felügyelt kulcsok visszaállításának tesztelése.

 

Key Vault-kulcsok visszaállítása az Azure-ban: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a Key Vault a helyreállítható Törlés engedélyezése a kulcsok véletlen vagy rosszindulatú törléssel szembeni megvédéséhez. Az Azure Event Hubs megköveteli, hogy az ügyfél által felügyelt kulcsok helyreállítva legyenek, és ne legyenek beállítva a törlés.

Az Event Hubs-adatgyűjtéshez használt Azure Storage-fiókhoz tartozó Soft delete konfigurálása. Vegye figyelembe, hogy ez a funkció még nem támogatott Azure Data Lake Storage Gen 2 esetében.

A Soft delete engedélyezése a Key Vaultban: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Key Vault beállítása kulcsokkal: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Az Azure Storage-Blobok Soft DELETE: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Ellenőrizze, hogy vannak-e írásos incidensekre vonatkozó válaszok, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének és kezelésének fázisait.

Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatának megtervezéséhez: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után. 

A Azure Security Center biztonsági kapcsolattartó beállítása: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

Folyamatos exportálás konfigurálása: https://docs.microsoft.com/azure/security-center/continuous-export

Riasztások továbbítása az Azure Sentinelbe: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

A Munkafolyamat-automatizálás és a Logic Apps konfigurálása: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: rendszeres penetrációs tesztelést végez az Azure-erőforrásokon, és gondoskodik az összes kritikus biztonsági vizsgálat 60 napon belüli szervizeléséről

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék meg a Microsoft-házirendeket: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .
A Microsoft által felügyelt felhő-infrastruktúrával,-szolgáltatásokkal és-alkalmazásokkal kapcsolatos további információkért tekintse meg a következő témakört: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További információ az [Azure biztonsági Alaptervekről](../security/benchmarks/security-baselines-overview.md)