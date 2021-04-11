---
title: Azure DevTest Labs Azure biztonsági alapterve
description: A Azure DevTest Labs biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 27a0d5b809480b2ce4aff36c5acd43c149ed5bb3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562834"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure DevTest Labs Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Azure DevTest Labsre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure DevTest Labs vonatkozó kapcsolódó útmutatás. A Azure DevTest Labs nem alkalmazható **vezérlők** ki vannak zárva.

Ha szeretné megtekinteni, hogyan Azure DevTest Labs teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure DevTest Labs biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutatás**: Azure DevTest Labs-erőforrások központi telepítésekor létre kell hoznia vagy használnia kell egy meglévő virtuális hálózatot. Győződjön meg arról, hogy a kiválasztott virtuális hálózat rendelkezik egy hálózati biztonsági csoporttal az alhálózatokra, valamint az alkalmazás megbízható portjaira és forrásaira konfigurált hálózati hozzáférés-vezérlőket. Ha a labor-erőforrások virtuális hálózattal vannak konfigurálva, nem nyilvánosan címezhető, és csak a virtuális hálózaton belülről érhetők el. Dönthet úgy is, hogy létrehoz egy hálózati elkülönített labort, ahol a laboratóriumi környezetek mellett a laboratóriumi erőforrások, például a Storage-fiók és a kulcstartók is teljesen el lesznek különítve, és csak a megadott végpontokon keresztül érhetők el. 

A szervezeti igényektől függően a Azure Firewall szolgáltatás segítségével központilag hozhat létre, kényszerítheti és naplózhatja az alkalmazás-és hálózati kapcsolati szabályzatokat az előfizetések és a virtuális hálózatok között.

- [Virtuális hálózat konfigurálása Azure DevTest Labshoz](devtest-lab-configure-vnet.md)

- [Hálózati elkülönített DevTest Labs-példány létrehozása](network-isolation.md)

- [Hálózati biztonsági csoport létrehozása biztonsági szabályokkal](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: hálózati biztonsági csoport központi telepítése azon a hálózaton, amelyen a Azure DevTest Labs-erőforrások telepítve vannak. Engedélyezze a hálózati biztonsági csoport adatfolyam-naplóit a hálózati biztonsági csoportokon a forgalom naplózásához.

NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) üzembe helyezése az Azure Resource Manager-sablonok használatával üzembe helyezett kritikus webalkalmazások előtt, a bejövő forgalom további ellenőrzéséhez. Diagnosztikai beállítás engedélyezése a WAF és a naplók betöltéséhez egy Storage-fiókba, az Event hub-ba vagy a Log Analytics-munkaterületre.

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure-Virtual Network (VNet) tesztkörnyezetben való üzembe helyezése fokozza a tesztkörnyezet biztonságát és elkülönítését. Az alhálózatok, a hozzáférés-vezérlési házirendek és más funkciók a hozzáférés korlátozását is segítik. Egy VNet való üzembe helyezéskor a Azure DevTest Labs nyilvánosan nem címezhető, és csak a VNet lévő virtuális gépekről és alkalmazásokból érhető el.

Az Azure-beli virtuális hálózatok DDoS standard szintű védelmének engedélyezése a DDoS-támadások elleni védelem érdekében. Az ismert kártékony IP-címekkel folytatott kommunikáció megtagadásához használja Azure Security Center integrált veszélyforrások felderítését.

Azure Firewall üzembe helyezése minden szervezet hálózati határain, a fenyegetések felderítése engedélyezve van, és a "riasztás és megtagadás" értékre van állítva a kártékony hálózati forgalom esetében. A NSG konfigurálásához használja a Azure Security Center igény szerinti hálózati hozzáférését, hogy korlátozza a végpontok a jóváhagyott IP-címekre való kitettségét korlátozott időtartamra. Azure Security Center adaptív hálózati korlátozással olyan NSG-konfigurációkat javasolhat, amelyek a portok és a forrás IP-címeket korlátozzák a tényleges forgalom és a veszélyforrások felderítése alapján.

- [Virtuális hálózat konfigurálása Azure DevTest Labshoz](devtest-lab-configure-vnet.md)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

- [Azure Security Center adaptív hálózat megerősítésének ismertetése](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center az időponthoz tartozó hálózati Access Control ismertetése](../security-center/security-center-just-in-time.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: a rendellenes tevékenységek kivizsgálásához engedélyezze Network Watcher csomagok rögzítését a tesztkörnyezet virtuális hálózatán. 

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a virtuális hálózaton üzembe helyezett Azure Firewall használata, amelyen engedélyezve van a veszélyforrások felderítése. Azure Firewall fenyegetés intelligencián alapuló szűréssel riasztást kaphat, és megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája. Ha a szervezete további funkciókat igényel a Azure Firewall számára, akkor válasszon ki egy megfelelő ajánlatot az Azure piactéren, amely támogatja az AZONOSÍTÓk/IP-címek funkciót a hasznos adatok vizsgálatára szolgáló funkciókkal.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy megtagadhatja a kártékony forgalmat.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)
 

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: Ha a DevTest Labs szolgáltatással Azure Resource Manager webalkalmazásokat tartalmazó környezeteket, helyezzen üzembe egy Azure-Application Gateway, amelyeken engedélyezve van a megbízható tanúsítványok használata HTTPS/TLS-vel.

- [Application Gateway üzembe helyezése](../application-gateway/quick-create-portal.md)

- [A Application Gateway konfigurálása a HTTPS használatára](../application-gateway/create-ssl-portal.md)

- [A 7. rétegbeli terheléselosztás és az Azure-webalkalmazás-átjárók ismertetése](../application-gateway/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: Virtual Network szolgáltatás-címkék használatával meghatározhatja a hálózati biztonsági csoportokon Azure Firewall vagy a DevTest Labs-erőforrásokhoz konfigurált hálózati hozzáférés-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Emellett az alkalmazás biztonsági csoportjaival is egyszerűsítheti az összetett biztonsági konfigurációt. Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

- [Az alkalmazás biztonsági csoportjai megismerése és használata](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával.

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például az Azure Resources Manager-sablonokat, a RBAC-vezérlőket és a házirendeket egyetlen terv definíciójában. A tervrajzot új előfizetésekre alkalmazhatja, és az irányítás és felügyelet finomhangolását a verziószámozás segítségével végezheti el.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a Azure DevTest Labs üzembe helyezéséhez társított hálózati erőforrásokhoz használjon címkéket, hogy logikailag szervezze őket a besorolásba. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti az erőforrás-konfigurációkat, és felderítheti az Azure-erőforrások módosításait. Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus erőforrások változásai megváltoznak.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrások időforrásait. Azonban a számítási erőforrások időszinkronizálási beállításait is kezelheti.

- [Windows rendszerű virtuális gépek időszinkronizálása az Azure-ban](../virtual-machines/windows/time-sync.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics-munkaterületre, Azure Event hub vagy Azure Storage-fiókba az archívumhoz. A tevékenységi naplók betekintést nyújtanak a Azure DevTest Labs példányain végrehajtott műveletekre a felügyeleti sík szintjén. Az Azure-beli tevékenység naplójának adatai alapján meghatározhatja, hogy a DevTest Labs-példányok felügyeleti síkon milyen műveleteket hajtson végre az írási műveletekhez (PUT, POST, DELETE).

- [Diagnosztikai beállítások létrehozása a platformnaplók és -metrikák más célhelyekre való küldéséhez](../azure-monitor/essentials/diagnostic-settings.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics-munkaterületre, Azure Event hub vagy Azure Storage-fiókba az archívumhoz. A tevékenységi naplók betekintést nyújtanak a Azure DevTest Labs példányain végrehajtott műveletekre a felügyeleti sík szintjén. Az Azure-tevékenység naplójának adatai alapján meghatározhatja a "mi, ki és mikor" minden írási művelethez (PUT, POST, DELETE) a DevTest Labs-példányok felügyeleti síkja szintjén.

- [Diagnosztikai beállítások létrehozása a platformnaplók és -metrikák más célhelyekre való küldéséhez](../azure-monitor/essentials/diagnostic-settings.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: Azure DevTest Labs virtuális gépeket (VM) hoznak létre és birtokolják az ügyfél. Tehát a szervezet feladata a figyelés. A számítási operációs rendszer figyeléséhez Azure Security Center is használhatja. Az operációs rendszer Security Center által összegyűjtött adatok közé tartozik az operációs rendszer típusa és verziója, az operációs rendszer (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek és a bejelentkezett felhasználó. A Log Analytics ügynök az összeomlási memóriaképek fájljait is gyűjti.

További információért tekintse át a következő cikkeket:

- [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Az Azure Security Center adatgyűjtés ismertetése](../security-center/security-center-enable-data-collection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitor a szervezet megfelelőségi előírásai alapján a Azure DevTest Labs példányokhoz társított log Analytics-munkaterületek naplózásának megőrzési időtartamát állíthatja be.

- [További információkért tekintse meg a következő cikket:](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics munkaterületre. Log Analytics lekérdezéseket futtathat a kifejezésekben, azonosíthatja a trendeket, elemezheti a mintákat, és számos más elemzést is elvégezhet a Azure DevTest Labs számára összegyűjtött tevékenységi naplók alapján.

További információért tekintse át a következő cikkeket:

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure-Tevékenységnaplók összegyűjtése és elemzése Log Analytics munkaterületen Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Azure log Analytics munkaterület használata a rendellenes tevékenységekkel kapcsolatos, a biztonsági naplókban és a Azure DevTest Labs kapcsolódó eseményeken való figyeléshez és riasztáshoz.

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket futtathat a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Szerepkörök Azure DevTest Labs](devtest-lab-add-devtest-user.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure Active Directory (Azure ad) nem az alapértelmezett jelszavak fogalma. Más Azure-erőforrások, amelyek jelszó megadását igénylik a bonyolultsági követelményekkel és a jelszó minimális hosszával, amely a szolgáltatástól függően eltérő lehet. Ön felelős harmadik féltől származó alkalmazásokért és piactér-szolgáltatásért, amelyek az alapértelmezett jelszavakat használhatják.

A DevTest Labs nem rendelkezik az alapértelmezett jelszavak fogalmával.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

- [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

- [Szerepkörök Azure DevTest Labs](devtest-lab-add-devtest-user.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az DevTest Labs a Azure Active Directory (Azure ad) szolgáltatást használja az Identitáskezelés kezeléséhez. Vegye figyelembe ezt a két fontos szempontot, amikor a felhasználók számára hozzáférést biztosít a DevTest Labs-alapú környezetekhez:
- Erőforrás-kezelés: hozzáférést biztosít a Azure Portal az erőforrások kezeléséhez (virtuális gépek létrehozásához, környezetek létrehozásához, elindításához, leállításához, újraindításához, törléséhez és összetevők alkalmazásához stb.). Az erőforrás-kezelés az Azure-ban az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával végezhető el. Szerepköröket rendelhet a felhasználókhoz, és beállíthatja az erőforrás-és hozzáférési szintű engedélyeket.
- Virtual Machines (hálózati szintű): az alapértelmezett konfigurációban a virtuális gépek helyi rendszergazdai fiókot használnak. Ha van elérhető tartomány (Azure Active Directory Domain Services (Azure AD DS), helyszíni tartomány vagy felhőalapú tartomány), a gépek csatlakoztathatók a tartományhoz. A felhasználók ezután használhatják a tartományon alapuló identitásokat a tartományhoz való csatlakozás használatával a gépekhez való csatlakozáshoz.

- [A DevTest Labs hivatkozási architektúrája](./devtest-lab-reference-architecture.md#architecture)

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bejelentkezéséhez és konfigurálásához konfigurált, többtényezős hitelesítéssel rendelkező emelt szintű hozzáférésű munkaállomások (mancsok) használata. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 
- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory (Azure ad) biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: csak a jóváhagyott helyekről származó Azure-erőforrások kezelése

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/overview-reports.md)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységekhez, naplózáshoz és kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik, hogy integrálható legyen a biztonsági információkkal és az Event Management (SIEM)/monitoring eszközzel.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. A riasztásokat Log Analytics munkaterületen belül is konfigurálhatja.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) kockázati és Identity Protection-funkciókkal konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata a laborokhoz való hozzáférés vezérléséhez Azure DevTest Labsban.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [A DevTest Labs szerepköreinek megismerése](devtest-lab-add-devtest-user.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel a DevTest Labs-példányok és egyéb kritikus vagy kapcsolódó erőforrások módosíthatók.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

- [Riasztások létrehozása a DevTest Labs tevékenység-naplózási eseményeihez](create-alerts.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (beleértve az DevTest Labs-erőforrásokat is) az előfizetéseken belül. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes enumerálni az előfizetések összes Azure-előfizetését és erőforrását.

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, amelyekkel a metaadatok logikailag rendezhetők a besorolások alapján.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Címkék konfigurálása a DevTest Labs számára](devtest-lab-add-tag.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a laborok és a laborokkal kapcsolatos erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézést, a felügyeleti csoportokat és a különálló előfizetéseket, valamint a különböző Labs-ket. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy az előfizetés gyorsan törölje a jogosulatlan erőforrásokat.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Labor létrehozása a DevTest Labs használatával](devtest-lab-create-lab.md)

- [Címkék létrehozása és használata](devtest-lab-add-tag.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása szervezeti igények szerint. Előfizetés-rendszergazdaként használhat adaptív alkalmazás-vezérlőket is Azure Security Center, amelyek segítségével meghatározhatja azokat az alkalmazásokat, amelyek számára engedélyezett a laboratóriumi gépek konfigurált csoportjain való futtatás. Ez a szolgáltatás az Azure-ban és a nem Azure-beli Windowsban (minden verzió, klasszikus vagy Azure Resource Manager) és linuxos gépen is elérhető.

- [Az adaptív alkalmazások vezérlésének engedélyezése](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával is lekérdezheti vagy felderítheti az előfizetéseken belüli erőforrásokat. Ez segíthet a magas biztonságon alapuló környezetekben, például a Storage-fiókokkal.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: a Azure Automation teljes körű irányítást biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során. Előfizetés-rendszergazdaként az Azure-beli virtuális gépek leltározásával automatizálhatja az előfizetésében található DevTest Labs-beli virtuális gépeken futó összes szoftver információinak gyűjtését. A szoftver neve, verziója, közzétevője és frissítési ideje tulajdonságok a Azure Portal érhetők el. A telepítés dátumának és egyéb információinak eléréséhez az ügyfélnek a vendég szintű diagnosztika engedélyezéséhez és a Windows-eseménynaplók Log Analytics munkaterületre való bekapcsolásához van szükség.

Amellett, hogy Change Trackingt használ a szoftveralkalmazások figyelésére, az adaptív alkalmazás-vezérlők a Azure Security Center a gépi tanulás segítségével elemzik a gépen futó alkalmazásokat, és létrehoznak egy engedélyezési listát ebből az intelligenciával. Ez a funkció nagy mértékben leegyszerűsíti az alkalmazások engedélyezési listájának házirend-szabályzatának konfigurálását és karbantartását, így elkerülhető, hogy a nemkívánatos szoftverek ne legyenek használatban a környezetben. Konfigurálhatja a naplózási módot vagy a kényszerített módot. A naplózási mód csak a védett virtuális gépeken lévő tevékenységeket naplózza. A kényszerített mód kikényszeríti a szabályokat, és ellenőrzi, hogy a nem engedélyezett alkalmazások le vannak-e tiltva. 

- [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

- [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

- [Az adaptív alkalmazások vezérlőinek ismertetése](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: a Azure Automation teljes körű irányítást biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során. Előfizetés-rendszergazdaként a DevTest Labs szolgáltatásban üzemeltetett virtuális gépekre telepített összes szoftver azonosításához Change Tracking használható. A jogosulatlan szoftverek eltávolításához saját folyamatot alkalmazhat, vagy használhatja Azure Automation állapot konfigurációját.

- [Bevezetés az Azure Automationbe](../automation/automation-intro.md)

- [A környezet változásainak követése a Change Tracking megoldással](../automation/change-tracking/overview.md)

- [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: az előfizetés-rendszergazdaként Azure Security Center adaptív alkalmazás-vezérlőelemekkel biztosíthatja, hogy csak a hitelesített szoftverek fussanak, és az összes jogosulatlan szoftver le legyen tiltva a DevTest Labs szolgáltatásban üzemeltetett Azure-beli virtuális gépeken.

- [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Referenciaanyagok:

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: az adaptív alkalmazás-vezérlés intelligens, automatizált, teljes körű megoldást kínál a Azure Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-beli és a nem Azure-beli (Windows és Linux rendszerű) DevTest Labs szolgáltatásban üzemeltetett alkalmazásokkal. Megjegyzés: ezt a beállítást a DevTest Labs szolgáltatásban üzemeltetett számítási erőforrások esetében kell beállítania előfizetés-rendszergazdaként. Harmadik féltől származó megoldás implementálása, ha ez a beállítás nem felel meg a szervezet követelményének.

- [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra a **Microsoft Azure felügyeleti** alkalmazás **blokkolási hozzáférésének** konfigurálásával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokban

**Útmutató**: a parancsfájlok típusától függően az operációs rendszerre jellemző konfigurációk vagy harmadik féltől származó erőforrások segítségével korlátozhatja, hogy a felhasználók képesek-e parancsfájlokat végrehajtani a DevTest Labs szolgáltatásban üzemeltetett virtuális gépeken belül. Azure Security Center adaptív alkalmazás-vezérlőelemekkel biztosíthatja, hogy csak a hitelesített szoftverek fussanak, és az összes jogosulatlan szoftver le legyen tiltva az alapul szolgáló Azure-beli virtuális gépeken.

- [A PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](/powershell/module/microsoft.powershell.security/set-executionpolicy?preserve-view=true&view=powershell-7)

- [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az Azure-környezetben üzembe helyezett magas kockázatú alkalmazások elkülöníthetők a virtuális hálózat, az alhálózat, az előfizetések, a felügyeleti csoportok és így tovább. és megfelelően védett Azure Firewall, webalkalmazási tűzfallal (WAF) vagy hálózati biztonsági csoporttal (NSG).

- [A DevTest Labs virtuális hálózatának konfigurálása](devtest-lab-configure-vnet.md)

- [Azure Firewall áttekintése](../web-application-firewall/overview.md)

- [Webalkalmazási tűzfal – áttekintés](../virtual-network/network-security-groups-overview.md)

- [Hálózati biztonság – áttekintés](security-baseline.md)

- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)

- [Útmutató az előfizetéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy-aliasok használata egyéni szabályzatok létrehozásához a DevTest Labs részeként létrehozott Azure-erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. A beépített Azure Policy-definíciókat is használhatja.

Emellett Azure Resource Manager lehetősége van a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni, hogy a konfigurációk megfeleljenek/túllépik a szervezete biztonsági követelményeit.

Az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően Azure Security Center javaslatokat is alkalmazhat.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: Azure Security Center javaslatok használata a biztonsági konfigurációk fenntartásához a DevTest Labs részeként létrehozott összes mögöttes számítási erőforráson. Emellett egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot-konfigurációt vagy DevTest Labs-összetevőket is használhat a szervezete által igényelt operációs rendszer biztonsági konfigurációjának létrehozásához.

- [Azure Security Center javaslatok figyelése](../security-center/security-center-recommendations.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

- [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md)

- [Virtuális merevlemez feltöltése és használata új Windows rendszerű virtuális gépek létrehozásához az Azure-ban](../virtual-machines/windows/upload-generalized-managed.md)

- [Linuxos virtuális gép létrehozása egyéni lemezről az Azure CLI-vel](../virtual-machines/linux/upload-vhd.md)

- [Egyéni lemezképek létrehozása és terjesztése több DevTest Labs-hoz](image-factory-save-distribute-custom-images.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: **Ha nem léteznek** szabályok a DevTest Labs részeként létrehozott Azure-erőforrások biztonságos beállításainak betartatásához, használja a Azure Policy **Megtagadás** és üzembe helyezés lehetőséget. Emellett Azure Resource Manager-sablonok használatával is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját.

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: a Azure Security Center ajánlásainak követése a sebezhetőségi felmérések elvégzéséhez a labor részeként létrehozott mögöttes Azure-beli számítási erőforrásokon. Emellett a szervezete által igényelt operációs rendszer biztonsági konfigurációjának fenntartása érdekében Azure Resource Manager sablonokat, egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot-konfigurációt is használhat. Használhatja a lemezkép-előállító megoldást is, amely egy olyan konfigurációs programkódot használó megoldás, amely rendszeresen készít és terjeszt képeket automatikusan az összes kívánt konfigurációval.

Emellett a Microsoft által közzétett Azure Marketplace virtuálisgép-rendszerképeket a Microsoft felügyeli és tartja karban.

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

- [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md)

- [Példaszkript egy VHD Azure-ba történő feltöltéséhez és új virtuális gép létrehozásához](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [Rendszerkép-előállító létrehozása a DevTest Labs szolgáltatásban](image-factory-create.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat és a kívánt állapotú konfigurációs parancsfájlokat. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára az Azure DevOps-vel való integráció esetén.

- [Az Azure Repos git oktatóanyaga](/azure/devops/repos/git/gitworkflow)

- [Az engedélyek és a csoportok](/azure/devops/organizations/security/about-permissions?preserve-view=true&tabs=preview-page&view=azure-devops)

- [Integráció a Azure DevTest Labs és az Azure DevOps munkafolyamat között](devtest-lab-dev-ops.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: Ha egyéni lemezképeket használ, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez. A megosztott képkatalógus használatával megoszthatja a lemezképeket olyan meghatározott laborokkal, amelyeknek szükségük van rá. A tárolói lemezképek esetében tárolhatja azokat Azure Container Registry és az Azure RBAC használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez.

- [Az Azure RBAC ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Az Azure RBAC konfigurálása](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Megosztott képgyűjtemény konfigurálása DevTest Labs számára](configure-shared-image-gallery.md)

- [Az Azure-RBAC megismerése Container Registry](../container-registry/container-registry-roles.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Azure Policy-Aliasok használatával egyéni szabályzatokat hozhat létre a DevTest Labs szolgáltatásban létrehozott Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet. Emellett Azure Automation használatával is telepítheti a konfigurációs módosításokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: Azure Automation állapot-konfiguráció a kívánt állapot-konfigurációs (DSC) csomópontok egyike a felhőben vagy a helyszíni adatközpontban. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják. Olyan egyéni összetevőt is megírhat, amely az összes tesztkörnyezetben telepíthető, és így biztosíthatja, hogy azok a szervezeti házirendek követésével legyenek végrehajtva. 

- [Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](../automation/automation-dsc-onboarding.md)

- [Egyéni összetevők létrehozása a DevTest Labs virtuális gépei számára](devtest-lab-artifact-author.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center segítségével elvégezheti az DevTest Labs szolgáltatásban létrehozott Azure-erőforrások alapkonfigurációjának vizsgálatait. Emellett az Azure-erőforrások konfigurációjának riasztására és naplózására Azure Policy is használhatja.

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: a Azure Security Center segítségével elvégezheti az operációs rendszer és a Docker-beállítások alapkonfigurációs vizsgálatait a laborban futtatott tárolók esetében.

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../security-center/container-security.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében.

- [Felügyelt identitás konfigurálása Azure Resource Manager környezetek telepítéséhez a DevTest Labs szolgáltatásban](use-managed-identities-environments.md)

- [Felügyelt identitás konfigurálása virtuális gépek üzembe helyezéséhez a DevTest Labs szolgáltatásban](enable-managed-identities-lab-vms.md)

- [Kulcstartó létrehozása](../key-vault/general/quick-create-portal.md)

- [Key Vault hitelesítés biztosítása felügyelt identitással](../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használata az Azure-szolgáltatások automatikus felügyelt identitással való ellátásához Azure Active Directory (Azure ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitás konfigurálása Azure Resource Manager környezetek telepítéséhez a DevTest Labs szolgáltatásban](use-managed-identities-environments.md)

- [Felügyelt identitás konfigurálása virtuális gépek üzembe helyezéséhez a DevTest Labs szolgáltatásban](enable-managed-identities-lab-vms.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: központilag felügyelt kártevő szoftver használata

**Útmutató**: az Azure-hoz készült Microsoft antimalware használatával folyamatosan figyelheti és védheti az erőforrásait. Linux esetén használjon harmadik féltől származó antimalware-megoldást.  Emellett a Azure Security Center veszélyforrások észlelését is használhatja az adatszolgáltatásokhoz a Storage-fiókokba feltöltött kártevők észlelése érdekében. 

- [Az Azure-hoz készült Microsoft antimalware konfigurálása](../security/fundamentals/antimalware.md) 

- [Fenyegetésvédelem az Azure Security Centerben](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft antimalware engedélyezve van azon a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például egy tesztkörnyezetben üzemeltetett Azure app Service), azonban nem fut a tartalomon. 

A nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage stb. 

A Storage-fiókokba feltöltött kártevők észleléséhez használja a Azure Security Center veszélyforrások észlelését az adatszolgáltatásokhoz. 

- [Az Azure-hoz készült Microsoft antimalware megismerése](../security/fundamentals/antimalware.md) 

- [Az adatszolgáltatások fenyegetés-észlelésének megismerése Azure Security Center](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: telepítésekor az Azure-hoz készült Microsoft antimalware szolgáltatás alapértelmezés szerint automatikusan telepíti a legújabb aláírást, platformot és motor-frissítéseket. Kövesse a Azure Security Centerban található ajánlásokat: "számítási &amp; alkalmazások", hogy a DevTest Labs mögöttes számítási erőforrások összes végpontja naprakész legyen a legújabb aláírásokkal. A Windows operációs rendszer további biztonsággal biztosítható, hogy a Microsoft Defender komplex veszélyforrások elleni védelmi szolgáltatásával, amely integrálható a Azure Security Centersal, a vírus-vagy kártevő-alapú támadások kockázatát korlátozza.

- [Az Azure-hoz készült Microsoft antimalware üzembe helyezése](../security/fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure DevTest Labs jelenleg nem támogatja a virtuális gépek biztonsági mentését és pillanatképeit. A DevTest Labs szolgáltatásban üzemeltetett Azure-beli virtuális gépeken azonban Azure Backup is engedélyezheti és konfigurálhatja. Emellett az automatikus biztonsági mentések esetében is konfigurálhatja a kívánt gyakoriságot és megőrzési időt, ha megfelelő hozzáféréssel rendelkezik a mögöttes számítási erőforrásokhoz. 

- [Az Azure virtuális gépek biztonsági mentésének áttekintése](../backup/backup-azure-vms-introduction.md)

- [Azure-beli virtuális gép biztonsági mentése a virtuális gép beállításaiból](../backup/backup-azure-vms-first-look-arm.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure DevTest Labs jelenleg nem támogatja a virtuális gépek biztonsági mentését és pillanatképeit. A DevTest Labs szolgáltatásban üzemeltetett mögöttes Azure-beli virtuális gépekről, illetve az ezekhez a példányokhoz csatolt felügyelt lemezekről a PowerShell vagy a REST API-k használatával készíthet pillanatképeket, feltéve, hogy megfelelő hozzáféréssel rendelkezik a mögöttes számítási erőforrásokhoz. A Azure Key Vaulton belüli ügyfelek által felügyelt kulcsok biztonsági mentését is elvégezheti.

Engedélyezze Azure Backup a célként megadott Azure-beli virtuális gépeken, valamint a kívánt gyakorisággal és megőrzési időtartamokkal. Magában foglalja a rendszerállapot teljes biztonsági mentését. Ha az Azure Disk Encryption szolgáltatást használja, az Azure virtuális gép biztonsági mentése automatikusan kezeli az ügyfél által felügyelt kulcsok biztonsági mentését.

- [Biztonsági mentés a titkosítást használó Azure-beli virtuális gépekről](../backup/backup-azure-vms-encryption.md)

- [Az Azure virtuális gépek biztonsági mentésének áttekintése](../backup/backup-azure-vms-introduction.md)

- [Az Azure virtuális gépek biztonsági mentésének áttekintése](../backup/backup-azure-vms-introduction.md)

- [Key Vault kulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Azure Backupon belüli tartalmak adatvisszaállításának rendszeres elvégzése. Ha szükséges, tesztelje a tartalom visszaállítását egy elkülönített virtuális hálózatra vagy előfizetésre. Emellett tesztelheti az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását is.

Ha az Azure Disk Encryption szolgáltatást használja, visszaállíthatja az Azure-beli virtuális gépet a lemez titkosítási kulcsaival. A lemezes titkosítás használata esetén visszaállíthatja az Azure-beli virtuális gépet a lemez titkosítási kulcsaival.

- [Biztonsági mentés a titkosítást használó Azure-beli virtuális gépekről](../backup/backup-azure-vms-encryption.md)

- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](../backup/backup-azure-restore-files-from-vm.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [Titkosított virtuális gépek biztonsági mentése és visszaállítása](../backup/backup-azure-vms-encryption.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a felügyelt lemezek Azure Backup használatával történő biztonsági mentésekor a virtuális gépek Storage Service encryption (SSE) inaktív állapotban vannak titkosítva. A Azure Backup a Azure Disk Encryption használatával titkosított Azure-beli virtuális gépek biztonsági mentését is elvégezheti. A Azure Disk Encryption a BitLocker titkosítási kulcsaival (BEKs) integrálható, amelyek titkos kulccsal rendelkeznek a Key vaultban. A Azure Disk Encryption Azure Key Vault kulcs-titkosítási kulcsokkal (KEK) is integrálva van. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához engedélyezze a Key Vault Soft-Delete.

- [Soft Delete a virtuális gépekhez](../backup/soft-delete-virtual-machines.md)

- [Azure Key Vault: a helyreállítható törlés áttekintése](../key-vault/general/soft-delete-overview.md)

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

**Útmutató**: a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett megadhatja a címkéket használó előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt. 

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md) 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a hézagokat, majd szükség szerint módosítsa a választ. 

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

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

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
