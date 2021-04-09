---
title: Virtual Network Azure biztonsági alapterve
description: A Virtual Network biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 420cd4a5b39f7c5864cf9802d02643e23c8ad24e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666559"
---
# <a name="azure-security-baseline-for-virtual-network"></a>Virtual Network Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) az Azure Virtual Network-ra vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure Virtual Network-ra vonatkozó kapcsolódó útmutatás. Az Azure Virtual Network nem alkalmazható **vezérlők** ki vannak zárva.

Ha szeretné megtekinteni, hogy az Azure hogyan Virtual Network teljes mértékben az Azure biztonsági teljesítményteszttel, tekintse meg a [teljes azure Virtual Network biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](../security/benchmarks/security-control-network-security.md)című témakörben talál.*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure-ban a hálózati erőforrások biztonságossá tételéhez használja a Security centert, és kövesse a hálózati védelmi javaslatok című témakört. 

Hálózati biztonsági csoport folyamatábrájának küldése Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést biztosíthat az Azure-felhőbe irányuló forgalomba. A Traffic Analytics lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalom működésének megértését, valamint a hálózati helytelen konfigurációkat. 

Azure Monitor naplók használatával betekintést biztosíthat a környezetbe. A munkaterületet fel kell használni az adatválogatásra és az elemzésre, és integrálni lehet más Azure-szolgáltatásokkal, például Application Insightsekkel és Security Centerokkal. 

Válassza ki az Azure Storage-fiókba vagy az Event hub-ba küldendő erőforrás-naplókat. A naplók elemzéséhez egy másik platform is használható. 

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az elosztott szolgáltatásmegtagadási (DDoS) standard szintű védelem engedélyezése az Azure-Virtual Network a DDOS-támadások elleni védelem érdekében.

Azure Firewall üzembe helyezése minden szervezet hálózati határain, és a fenyegetésekkel kapcsolatos intelligencia-alapú Szűrés engedélyezve van, és úgy van beállítva, hogy a "riasztás és megtagadás" a kártékony hálózati forgalmat.

Security Center veszélyforrások elleni védelmi funkcióinak használatával észlelheti az ismert kártékony IP-címekkel folytatott kommunikációt.

Alkalmazza Security Center adaptív hálózati korlátozási javaslatait a hálózati biztonsági csoportok olyan beállításaira, amelyek a portok és a forrás IP-címeket korlátozzák a tényleges forgalom és a veszélyforrások felderítése alapján. 

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall fenyegetés intelligencia-alapú szűrés](../firewall/threat-intel.md)

- [Veszélyforrások elleni védelem Security Center](../security-center/azure-defender.md)

- [Adaptív hálózati megerősítés Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: a hálózati csomagok rögzítésére szolgáló, általánosan elérhető csomagok rögzítési eszközein kívül használja a VPN Gateway csomagjának rögzítését. 

Áttekintheti az ügynök-alapú vagy NVA-megoldásokat is, amelyek lehetővé teszik a terminál-hozzáférési pont (TAP) vagy a hálózat láthatósági funkcióinak használatát az Azure Marketplace-ajánlatokban elérhető Packet Broker partneri megoldásokon keresztül.

- [A csomagok rögzítésének konfigurálása a VPN-átjárók számára](../vpn-gateway/packet-capture.md) 

- [Hálózati virtuális berendezések partnere](https://azure.microsoft.com/solutions/network-appliances)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a virtuális hálózaton üzembe helyezett Azure Firewall használata, amelyen engedélyezve van a veszélyforrások felderítése. Azure Firewall veszélyforrások felderítésére szolgáló szűréssel riasztást használhat, vagy megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló és onnan érkező forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája. 

Az Azure piactéren olyan megfelelő ajánlatot is kiválaszthat, amely támogatja az AZONOSÍTÓk/IP-címek funkcióit a hasznos adattartalom-ellenőrzési funkciók használatával.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy megtagadhatja a kártékony forgalmat.

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használjon Virtual Network szolgáltatás-címkéket. A szolgáltatás címkéi a biztonsági szabályok létrehozásakor a megadott IP-címek helyett használhatók. Engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát úgy, hogy megadja a szolgáltatási címke nevét (például ApiManagement) a szabály megfelelő forrás vagy cél mezőjében. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Az alkalmazás biztonsági csoportjaival egyszerűsítheti az összetett biztonsági konfiguráció használatát. Az alkalmazás biztonsági csoportjai lehetővé teszik a hálózati biztonság konfigurálását az alkalmazások struktúrájának természetes kiterjesztéseként. Ez lehetővé teszi a virtuális gépek csoportosítását és a hálózati biztonsági házirendek definiálását ezen csoportok alapján.

- [A szolgáltatási címkék megismerése és használata](service-tags-overview.md)

- [Az alkalmazás biztonsági csoportjai megismerése és használata](./network-security-groups-overview.md#application-security-groups)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy és a beépített hálózati házirend-definíciók áttekintése a megvalósításhoz.

Tekintse meg a virtuális hálózatokkal kapcsolatos elérhető biztonsági javaslatokat tartalmazó Security Center alapértelmezett házirendjét.

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) hozzárendelései és a szabályzatok egyetlen tervezet-definícióban való kicsomagolásával. A Azure Blueprint az új előfizetésekre alkalmazható a részletes vezérléshez és a verziószámozáshoz. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network) 

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

- [Azure Security Center figyelésének engedélyezése](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: hálózati biztonsági csoportokhoz és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz használható címkék használata. A "Description" (Leírás) mezővel megtudhatja az üzleti igényeket, az időtartamot és a többi olyan szabályt, amely engedélyezi a hálózatra irányuló, illetve az egyes hálózati biztonsági csoportokra vonatkozó szabályok elérését.
A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

Válassza az Azure PowerShell vagy az Azure CLI lehetőséget, ha a címkék alapján szeretne műveleteket keresni vagy végrehajtani az erőforrásokon.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti az erőforrás-konfigurációkat, és felderítheti a virtuális hálózat módosításait. Hozzon létre riasztásokat a Azure Monitoron belül, amely akkor aktiválódik, amikor a kritikus erőforrások változása történik.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a Azure monitor engedélyezése a naplózási és a tevékenységi naplók eléréséhez, beleértve az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célhelyek és más hasznos elemeket. 

Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz.
Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure monitor engedélyezése a naplózási és a tevékenységi naplók eléréséhez, beleértve az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célhelyek és más hasznos elemeket.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint. Használja az Azure Storage-fiókokat a biztonsági naplók tárolási megőrzésének hosszú távú és archiválási tárolására.

- [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre és a rendszeres eredményekre vonatkozó naplók elemzése és figyelése. Használja Azure Monitor Log Analytics munkaterületét az elemzések lekérdezéséhez és elvégzéséhez, és használja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. 

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Log Analytics munkaterület ismertetése](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Security Center használata log Analytics munkaterülettel a biztonsági naplókban és eseményekben észlelt rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztáshoz.

Azt is megteheti, hogy az Azure Sentinel-be vagy egy harmadik féltől származó SIEM-t is engedélyez, és riasztást küld.

- [Riasztások kezelése Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: külső gyártótól származó megoldás implementálása az Azure Marketplace-ről a DNS-naplózási megoldáshoz, a szervezeti igényeknek megfelelően.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure Active Directory (Azure ad) beépített rendszergazdai szerepkörök használata, amelyek explicit módon rendelhetők hozzá, és lekérdezhető. 

Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Security Center identitás-és hozzáférés-kezelési szolgáltatását.

A Microsoft szolgáltatásainak és Azure Resource Managerának Azure AD Privileged Identity Management Kiemelt szerepkörökkel való használatának engedélyezése igény szerint. 

- [További információ a Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: használja az egyszeri bejelentkezést Azure Active Directory (Azure ad) használatával, és ne konfigurálja a különálló önálló hitelesítő adatokat. Használja Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](../active-directory/manage-apps/what-is-single-sign-on.md) 

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory (Azure AD) multi-Factor Authentication (MFA) engedélyezése és a Security Center identitás-és hozzáférés-kezelési javaslatainak követése.

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: az Azure hálózati erőforrásainak bejelentkezni és elérésére konfigurált, multi-Factor Authentication (MFA) jogosultsági szintű hozzáférési munkaállomások használata.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory (Azure ad) kockázati észlelések használatával tekintheti meg a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket. 

Security Center kockázat észlelésével kapcsolatos riasztások betöltése Azure Monitor és egyéni riasztások/értesítések konfigurálása a műveleti csoportokkal.

- [Security Center kockázati észlelések ismertetése (gyanús tevékenység)](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [Műveleti csoportok konfigurálása egyéni riasztásokhoz és értesítésekhez](../azure-monitor/alerts/action-groups.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: az Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként a szolgáltatásaihoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz, valamint a felhasználók hitelesítő adatainak tárolásához és kiszűréséhez is.  

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) használata az elavult fiókok felderítését segítő naplók biztosításához. 

Az Azure Identity hozzáférési felülvizsgálatok a csoporttagságok, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések hatékony kezelése érdekében végezhetők el. A felhasználói hozzáférés rendszeres felülvizsgálata szükséges ahhoz, hogy csak az aktív felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: az Azure Active Directory (Azure ad) bejelentkezési tevékenység, a naplózási és a kockázati Eseménynapló-források integrálása bármely Siem vagy monitoring eszközzel a hozzáférése alapján. 

Egyszerűsítse ezt a folyamatot Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével. Minden kívánt riasztás konfigurálható Log Analytics munkaterületen belül.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) kockázatkezelési és identitás-védelmi funkcióinak használatával konfigurálhatja a virtuális hálózat felhasználói identitásával kapcsolatos gyanús műveleteket. Minden további vizsgálathoz betöltheti az összes adatot az Azure Sentinelbe.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](../security/benchmarks/security-control-data-protection.md)című témakörben talál.*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Győződjön meg arról, hogy a virtuális hálózatokban lévő Azure-erőforrásokhoz csatlakozó ügyfelek képesek legyenek egyeztetni a TLS 1,2-es vagy újabb verzióit. Kövesse Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat. 

A Microsoft számos lehetőséget kínál, amelyeket az ügyfelek használhatnak az Azure-hálózaton belüli, az interneten és a végfelhasználón kívüli forgalomban tárolt adatforgalom biztonságossá tételéhez. Ezek közé tartoznak a virtuális magánhálózatok (IPsec/IKE-titkosítást használó), a Transport Layer Security (TLS) 1,2-es vagy újabb (az Azure-összetevők, például a Application Gateway vagy az Azure bejárati ajtó), a protokollok közvetlenül az Azure-beli virtuális gépeken (például Windows IPsec vagy SMB) keresztül történő kommunikáció.

Emellett az Azure-adatközpontok közötti, a MACsec (az adatkapcsolati rétegben található IEEE standard) használatával az összes Azure-forgalom esetében engedélyezve van az adatok titkosítása és integritásának biztosítása.

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC 

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az adat-és erőforrásokhoz való hozzáférés kezelésére. Ellenkező esetben használja a szolgáltatás-specifikus hozzáférés-vezérlési metódusokat. 

Válasszon olyan beépített szerepköröket, mint a tulajdonos, a közreműködő vagy a hálózati közreműködő, és rendelje hozzá a szerepkört a megfelelő hatókörhöz. Például a virtuális hálózat képességeinek egy részhalmazát hozzárendelheti a virtuális hálózatokhoz szükséges konkrét engedélyekkel a szerepkörök bármelyikéhez. 

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [Virtuális hálózatok tervezése](virtual-network-vnet-plan-design-arm.md#permissions)

- [Az Azure beépített szerepköreinek áttekintése](../role-based-access-control/built-in-roles.md#networking)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplóinak használatával riasztásokat hozhat létre, amelyekkel a kritikus Azure-erőforrások, például a virtuális hálózatok és a hálózati biztonsági csoportok módosítására kerül sor.

- [Hálózati biztonsági csoport diagnosztikai naplózása](virtual-network-nsg-manage-log.md)

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph segítségével lekérdezheti és felderítheti az összes hálózati erőforrást, például a virtuális hálózatokat és az előfizetések alhálózatait. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md) 

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a virtuális hálózat és a kapcsolódó erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézés, a felügyeleti csoportok és a különálló előfizetések használatát. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md) 

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md) 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: létre kell hoznia egy leltárt a jóváhagyott Azure-erőforrásokról és jóváhagyott szoftverekről a számítási erőforrásokhoz a szervezeti igényeknek megfelelően.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok 

- Engedélyezett erőforrástípusok 

Az előfizetéseken belüli erőforrások lekérdezése vagy felderítése magas biztonságú környezetekben, például az Azure Storage-fiókokkal rendelkező Azure Resource Graphban. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure Policy minta – beépített virtuális hálózatok](./policy-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: az erőforrás-létrehozás vagy-használat megakadályozása Azure Policy a szervezet házirendjei által igényelt módon. Folyamatok implementálása a jogosulatlan erőforrások eltávolításához.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok 

- Engedélyezett erőforrástípusok 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

- [Azure Policy minta – beépített virtuális hálózatok](./policy-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók interakcióját Azure Resource Manager az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure hálózati erőforrások konfigurációjának naplózásához vagy betartatásához, valamint a beépített Azure Policy-definíciók használatát is.

Exportálja a Build sablonjait Azure Resource Manager JavaScript Object Notation (JSON) formában, és ellenőrizze, hogy a konfigurációk megfelelnek-e a szervezet biztonsági követelményeinek, vagy meghaladják-e azokat.

A Security Center ajánlásainak megvalósítása az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minta – beépített virtuális hálózatok](./policy-reference.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: Azure Resource Manager-sablonok és Azure-házirendek használata a virtuális hálózathoz és a kapcsolódó erőforrásokhoz társított Azure-erőforrások biztonságos konfigurálásához.  Azure Resource Manager sablonok a virtuális gépek Azure-erőforrásokkal együtt történő üzembe helyezéséhez használt JSON-alapú fájlok (JavaScript Object Notation). A Microsoft végzi el a karbantartást az alapsablonokon.  

Az Azure-erőforrások biztonságos beállításainak érvénybe léptetéséhez használja a Azure Policy [deny] és a [telepítés ha nem létezik] hatásokat.

- [Azure Resource Manager sablonok létrehozásával kapcsolatos információk](../virtual-machines/windows/ps-template.md) 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Azure Resource Manager-sablonminták virtuális hálózathoz](template-samples.md)

- [Azure Policy minta – beépített virtuális hálózatok](./policy-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat, a kívánt állapotú konfigurációs parancsfájlokat. és így tovább.

Engedélyekkel kell rendelkeznie az Azure-DevOps felügyelni kívánt erőforrások eléréséhez, például a kód, a buildek és a munkahelyi nyomkövetés számára. A legtöbb engedély a beépített biztonsági csoportokon keresztül adható meg. Az Azure DevOps integrált, Active Directory vagy a Team Foundation Server-integrációval rendelkező felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára is engedélyezheti vagy megtagadhatja az engedélyeket.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Az Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvényesítéséhez, illetve az egyes erőforrásokhoz kapcsolódó beépített szabályzat-definíciók létrehozásához. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

- [Azure Policy minta – beépített virtuális hálózatok](./policy-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: az Azure Virtual Network és a kapcsolódó erőforrások alapkonfigurációjának vizsgálatához használja a Security Center. Az Azure-erőforrások konfigurációjának riasztására és naplózására Azure Policy használható.

- [Javaslatok szervizelése Security Center](../security-center/security-center-remediate-recommendations.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minta – beépített virtuális hálózatok](./policy-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault használatával egyszerűbbé és biztonságossá teheti az azure-Virtual Network üzemeltetett Azure-erőforrások titkos kezelését.

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Key Vault létrehozása](../key-vault/secrets/quick-create-portal.md) 

- [Key Vault hitelesítés biztosítása felügyelt identitással](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: virtuális hálózat és kapcsolódó erőforrások üzembe helyezése Azure Resource Manager használatával. A Azure Resource Manager lehetővé teszi olyan sablonok exportálását, amelyek biztonsági mentésként használhatók a virtuális hálózat és a kapcsolódó erőforrások visszaállításához.  A Azure Automation használatával rendszeresen meghívhatja a Azure Resource Manager sablon exportálási API-ját.

- [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager-sablonminták virtuális hálózathoz](template-samples.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Erőforráscsoportok – sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

- [Bevezetés a Azure Automationba](../automation/automation-intro.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: virtuális hálózat és kapcsolódó erőforrások üzembe helyezése Azure Resource Manager használatával. A Azure Resource Manager lehetővé teszi olyan sablonok exportálását, amelyek biztonsági mentésként használhatók a virtuális hálózat és a kapcsolódó erőforrások visszaállításához. A Azure Automation használatával rendszeresen meghívhatja a Azure Resource Manager sablon exportálási API-ját. Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül.

- [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager-sablonminták virtuális hálózathoz](template-samples.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Erőforráscsoportok – sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

- [Bevezetés a Azure Automationba](../automation/automation-intro.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Azure Resource Manager-sablonok egy elkülönített előfizetésre való üzembe helyezése, valamint az ügyfél által felügyelt kulcsok biztonsági mentésének tesztelése.

- [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például az egyéni Azure Policy-definíciók és a Azure Resource Manager-sablonok. 

Engedélyek megadása vagy megtagadása adott felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az Azure DevOps integrálva van, vagy Active Directory, ha a Team Foundation Server integrálva van.  

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja az ügyfél által felügyelt kulcsok használatát.   

A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze Soft-Delete és törölje a védelmet a Key Vaultban.  

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [A Soft-Delete engedélyezése és a védelem kiürítése Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Az Azure Storage-blobok helyreállítható törlése](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](../security/benchmarks/security-control-incident-response.md)című témakörben talál.*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.  

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [A NIST számítógépes biztonsági incidensek kezelésének útmutatója a saját incidensekre vonatkozó válaszadási terv létrehozásához nyújtott támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Egyértelműen megjelölheti az előfizetéseket (például éles vagy nem éles üzemben) címkék használatával, és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások Security Center](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. 

A riasztásokat a Security Center adatösszekötővel is továbbíthatja az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)