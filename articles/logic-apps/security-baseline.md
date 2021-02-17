---
title: Logic Apps Azure biztonsági alapterve
description: A Logic Apps biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cf4e02f925e0c8c4024137589c0c9c4074c0aa11
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596751"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Logic Apps Azure biztonsági alapterve

Az Logic Apps Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](../security/benchmarks/security-control-network-security.md)című témakörben talál.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: a "globális", több-bérlős Logic apps szolgáltatásban futó összekötőket a Microsoft telepíti és kezeli. Ezek az összekötők eseményindítókat és műveleteket biztosítanak a Cloud Services, a helyszíni rendszerek vagy mindkettő eléréséhez, beleértve az Office 365, az Azure Blob Storage, a SQL Server, a Dynamics, a Salesforce, a SharePoint és egyéb szolgáltatásokat. A AzureConnectors szolgáltatás címkéjét a hálózati biztonsági csoport szabályainak megadásakor vagy Azure Firewall a kapcsolódó erőforrásokhoz való hozzáférés engedélyezéséhez használhatja.

Azon logikai alkalmazások esetében, amelyeknek közvetlen hozzáférésre van szükségük egy Azure-beli virtuális hálózat erőforrásaihoz, létrehozhat egy integrációs szolgáltatási környezetet (ISE), ahol a logikai alkalmazásokat dedikált erőforrásokon hozhatja létre, helyezheti üzembe és futtathatja. Néhány Azure-beli virtuális hálózat privát végpontokat (Azure Private-hivatkozást) használ az Azure-beli (Azure-beli) szolgáltatások, például az Azure Storage, a Azure Cosmos DB, a Azure SQL Database, a partner Services vagy az Azure-ban üzemeltetett ügyfélszolgálatok számára való hozzáférés biztosításához. Ha a logikai alkalmazásoknak hozzá kell férniük a privát végpontokat használó virtuális hálózatokhoz, létre kell hoznia, telepítenie és futtatnia kell ezeket a logikai alkalmazásokat egy ISE-ben.

Az ISE létrehozásakor dönthet úgy, hogy belső vagy külső hozzáférési végpontokat használ. A kiválasztott beállítás határozza meg, hogy az ISE-beli Logic apps-beli kérelem vagy webhook-eseményindítók fogadhatnak-e hívásokat a virtuális hálózaton kívülről is. A belső és a külső hozzáférési végpontok is befolyásolhatják, hogy megtekinthetők-e a logikai alkalmazás futtatási előzményei, beleértve a futtatások bemeneteit és kimeneteit a virtuális hálózaton belül vagy kívül.

Győződjön meg arról, hogy az ISE-hez kapcsolódó összes virtuális hálózati alhálózat üzembe helyezése egy hálózati biztonsági csoporttal rendelkezik, amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel van alkalmazva. Ha egy ISE-ben helyezi üzembe a logikai alkalmazásokat, használja a privát hivatkozást. Az Azure Private-hivatkozás lehetővé teszi, hogy a virtuális hálózat egy privát végpontján keresztül hozzáférjen az Azure Pásti-szolgáltatásokhoz és az Azure által üzemeltetett felhasználói/partneri szolgáltatásokhoz. Ha konkrét használati esettel rendelkezik, a Azure Firewall megvalósításával is teljesítheti ezt a követelményt. A biztonsági szabályok beállításakor a bonyolultság csökkentése érdekében az adott Azure-szolgáltatáshoz tartozó IP-cím-előtagokat képviselő szolgáltatás-címkéket használjon.

- [Logic Apps-összekötők ismertetése](../connectors/apis-list.md)

- [A szolgáltatási címkék megismerése az Azure-ban](../virtual-network/service-tags-overview.md)

- [Az Azure Virtual Network-erőforrásokhoz való hozzáférés megismerése Azure Logic Apps az Integration Service Environment (ISEs) használatával](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Virtual Network szolgáltatási végpontok ismertetése](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Az Azure privát hivatkozásának megismerése](../private-link/private-link-overview.md)

- [Az ISE Endpoint Access ismertetése](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [Biztonsági konfigurációval rendelkező NSG létrehozása](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)

- [Az ISE hozzáférésének engedélyezése](connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: Ha a Logic Apps szolgáltatást egy külső hozzáférési pontot használó integrációs szolgáltatási környezetben (ISE) futtatja, akkor a hálózati biztonsági csoport (NSG) segítségével csökkentheti az adatkiszűrése kockázatát. NSG-adatforgalmi naplók engedélyezése és naplók küldése Azure Storage-fiókba forgalom-ellenőrzés céljából. NSG-naplókat is küldhet Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [Az ISE Endpoint Access ismertetése](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: Ha a logikai alkalmazás egy kérelem-alapú triggert használ, amely fogadja a bejövő hívásokat vagy kéréseket, például a kérést vagy a webhookot, korlátozhatja a hozzáférést, hogy csak a jogosult ügyfelek tudják meghívni a logikai alkalmazást.

Ha a Logic apps-t egy integrációs szolgáltatási környezetben (ISE) futtatja, engedélyezze az ISE-hez társított virtuális hálózat DDoS Protection szabványát a DDoS-támadások elleni védelemhez. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

Azure Firewall üzembe helyezése minden szervezet hálózati határain, a fenyegetések felderítése engedélyezve van, és a "riasztás és megtagadás" értékre van állítva a kártékony hálózati forgalom esetében.

A NSG konfigurálásához használja a Azure Security Center igény szerinti hálózati hozzáférését, hogy korlátozza a végpontok a jóváhagyott IP-címekre való kitettségét korlátozott időtartamra.

Azure Security Center adaptív hálózati korlátozással olyan NSG-konfigurációkat javasolhat, amelyek a portok és a forrás IP-címeket korlátozzák a tényleges forgalom és a veszélyforrások felderítése alapján.

- [Bejövő hívások biztonságossá tétele Logic Apps](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Bejövő IP-címek korlátozása](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

- [Azure Security Center adaptív hálózat megerősítésének ismertetése](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center az időponthoz tartozó hálózati Access Control ismertetése](../security-center/security-center-just-in-time.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Ha a Logic Apps szolgáltatást egy külső hozzáférési pontot használó integrációs szolgáltatási környezetben (ISE) futtatja, akkor a hálózati biztonsági csoport (NSG) segítségével csökkentheti az adatkiszűrése kockázatát. NSG-adatforgalmi naplók engedélyezése és naplók küldése Azure Storage-fiókba forgalom-ellenőrzés céljából. NSG-naplókat is küldhet Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

A hálózati forgalomra vonatkozó további védelem és információk biztosításához tekintse meg a hozzáférési naplókat, amelyek csak akkor jönnek létre, ha mindegyik Application Gateway-példányon engedélyezte őket. A napló használatával megtekintheti Application Gateway hozzáférési mintákat, és elemezheti a fontos információkat. Ide tartozik a hívó IP-címe, a kért URL-cím, a válasz késése, a visszatérési kód és a kimenő bájtok.

Ellenkező esetben egy külső gyártótól származó megoldást is használhat a piactéren, hogy megfeleljen ennek a követelménynek.

- [Az ISE Endpoint Access ismertetése](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [API Management integrálása belső VNET a Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [A WAF-hozzáférési naplók megismerése](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: válasszon ki egy olyan ajánlatot az Azure piactéren, amely támogatja az azonosítók/IP-címek funkciót a hasznos adatok vizsgálatával.  Ha a betörési észlelés és/vagy a tartalom-ellenőrzésen alapuló megelőzés nem követelmény, akkor az Azure Firewall a veszélyforrások felderítésére használható. Azure Firewall fenyegetés intelligencián alapuló szűréssel riasztást kaphat, és megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy megtagadhatja a kártékony forgalmat.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: Ha logikai alkalmazásokat futtat egy integrációs szolgáltatási környezetben (ISE), telepítse az Azure Application Gateway-t.

- [API Management integrálása belső VNET a Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [A Application Gateway konfigurálása a HTTPS használatára](../application-gateway/create-ssl-portal.md) 

- [A 7. rétegbeli terheléselosztás és az Azure-webalkalmazás-átjárók ismertetése](../application-gateway/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Azure Logic apps példányokhoz hozzáférést igénylő erőforrásokhoz használjon virtuális hálózati szolgáltatás címkéit a hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlés definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például LogicApps, LogicAppsManagement) megadásával egy szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [További információ a szolgáltatási címkék használatáról](../virtual-network/service-tags-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a Azure Logic apps-példányokhoz kapcsolódó hálózati erőforrásokhoz Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Logic" és a "Microsoft. Network" névterekben egyéni szabályzatok létrehozásához az Azure Logic apps-példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Olyan beépített szabályzat-definíciókat is használhat, mint például a következők:

- A Logic Apps lévő diagnosztikai naplókat engedélyezni kell

- DDoS Protection a standardot engedélyezni kell

Az Azure-tervezetek segítségével leegyszerűsítheti a nagy léptékű Azure-környezetek kiépítését a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek alapján, egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [Logic Apps Azure Policy definícióinak listája](./policy-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használatával figyelheti a hálózati erőforrás-konfigurációkat, és azonosíthatja a Azure Logic apps-példányokhoz kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrásokhoz használt időforrást, például az Azure Logic apps a naplókban található időbélyegek esetében.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a logikai alkalmazásokkal kapcsolatos részletes hibakeresési információk a futtatókörnyezetben való futtatásához beállíthatja és használhatja Azure monitor naplókat a futásidejű adatok és események adatainak, például az események aktiválásának, az események futtatásának és a műveleti eseményeknek a log Analytics munkaterületen való rögzítéséhez és tárolásához. Az Azure Monitor segít monitorozni a felhőt és a helyszíni környezeteket, így egyszerűbb fenntartani ezek rendelkezésre állását és teljesítményét. Azure Monitor naplók használatával olyan napló-lekérdezéseket hozhat létre, amelyek segítenek az információk gyűjtésében és áttekintésében. A diagnosztikai adatait más Azure-szolgáltatásokkal is használhatja, mint például az Azure Storage és az Azure Event Hubs.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor naplók beállítása és diagnosztikai adatok összegyűjtése Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a logikai alkalmazásokkal kapcsolatos részletes hibakeresési információk a futtatókörnyezetben való futtatásához beállíthatja és használhatja Azure monitor naplókat a futásidejű adatok és események adatainak, például az események aktiválásának, az események futtatásának és a műveleti eseményeknek a log Analytics munkaterületen való rögzítéséhez és tárolásához. Az Azure Monitor segít monitorozni a felhőt és a helyszíni környezeteket, így egyszerűbb fenntartani ezek rendelkezésre állását és teljesítményét. Azure Monitor naplók használatával olyan napló-lekérdezéseket hozhat létre, amelyek segítenek az információk gyűjtésében és áttekintésében. A diagnosztikai adatait más Azure-szolgáltatásokkal is használhatja, mint például az Azure Storage és az Azure Event Hubs.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor naplók beállítása és diagnosztikai adatok összegyűjtése Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: logikai alkalmazás létrehozása és futtatása után megtekintheti a logikai alkalmazás futtatási állapotát, futtathatja az előzményeket, az aktiválási előzményeket és a teljesítményt. A valós idejű események figyelése és a szélesebb körű hibakeresés érdekében Azure Monitor naplók használatával állítsa be a logikai alkalmazás diagnosztikai naplózását. Ez az Azure-szolgáltatás segít a felhő és a helyszíni környezetek monitorozásában, így könnyebben megtarthatja a rendelkezésre állást és a teljesítményt. Ezután megkeresheti és megtekintheti az eseményeket, például az események aktiválását, az események futtatását és a műveleti eseményeket. Ha ezeket az információkat Azure Monitor-naplókban tárolja, létrehozhat olyan napló-lekérdezéseket, amelyek segítségével megkeresheti és elemezheti ezeket az adatokat. Ezt a diagnosztikai szolgáltatást más Azure-szolgáltatásokkal, például az Azure Storage-val és az Azure Event Hubs is használhatja.

A Azure Monitor a szervezet megfelelőségi előírásai szerint állítsa be a Azure Logic Apps példányokhoz társított naplók naplójának megőrzési időtartamát.

- [A futtatási állapot figyelése, az aktiválási előzmények áttekintése és a Azure Logic Apps riasztások beállítása](monitor-logic-apps.md)

- [Napló-megőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a logikai alkalmazás naplózásának beállításához engedélyezheti log Analytics a logikai alkalmazás létrehozásakor, vagy a meglévő logikai alkalmazások log Analytics munkaterületén is telepítheti a Logic apps felügyeleti megoldást. Ez a megoldás összesített információt biztosít a logikai alkalmazás futtatásához, és konkrét részleteket tartalmaz, például az állapotot, a végrehajtási időt, az újraküldési állapotot és a korrelációs azonosítókat. Ezután a naplózás engedélyezéséhez és a lekérdezések létrehozásához hozzon létre Azure Monitor naplókat.

Engedélyezheti az Azure műveletnapló diagnosztikai beállításait is, és elküldheti a naplókat egy Log Analytics munkaterületre. Lekérdezéseket végezhet Log Analytics a kifejezések kereséséhez, azonosíthatja a trendeket, elemezheti a mintákat, és számos más elemzést is biztosíthat a Azure Logic Apps számára összegyűjtött tevékenységi napló adatai alapján.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Azure Monitor naplók beállítása és diagnosztikai adatok összegyűjtése Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Azure-Tevékenységnaplók összegyűjtése és elemzése Log Analyticsban Azure Monitor](../azure-monitor/essentials/activity-log.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a biztonsági naplókban és eseményekben található rendellenes tevékenységekkel kapcsolatos figyelési és riasztási Azure Security Center használata log Analytics használatával.

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; Azure Logic Apps nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Azure Logic Apps nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Active Directory (ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

A Azure Active Directory (Azure AD) által védett egyéb erőforrások egyszerű eléréséhez és az identitás bejelentkezés nélküli hitelesítéséhez a logikai alkalmazás felügyelt identitást (korábban Managed Service Identity vagy MSI) is használhat a hitelesítő adatok és a titkos kódok helyett. Az Azure kezeli Ön helyett ezt az identitást, és segít biztonságban tartani az Ön hitelesítő adatait, mivel így nincs szükség titkos kulcsok megadására és rendszeres módosítására.

A logikai alkalmazás minden kérelem végpontja közös hozzáférési aláírással (SAS) rendelkezik a végpont URL-címében. Ha egy kérelem-alapú trigger végpontjának URL-címét megosztja más felekkel, létrehozhat olyan visszahívási URL-címeket, amelyek meghatározott kulcsokat használnak, és lejárati dátummal rendelkeznek. Így zökkenőmentesen válthat kulcsokat, vagy korlátozhatja a hozzáférést a logikai alkalmazás aktiválására egy adott TimeSpan alapján.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Az Azure-erőforrásokhoz való hozzáférés hitelesítése felügyelt identitások használatával Azure Logic Apps](create-managed-service-identity.md)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [A Azure Logic Apps hozzáférésének és adatvédelmének védelme SAS használatával](logic-apps-securing-a-logic-app.md#sas)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Azure Active Directory és a Azure Logic apps nem rendelkezik az alapértelmezett jelszavak fogalmával.

Ha alapszintű hitelesítést használ, meg kell adnia egy felhasználónevet és egy jelszót. A hitelesítő adatok létrehozásakor ügyeljen arra, hogy erős jelszót állítson be a hitelesítéshez.

Ha kódot használ, ne tárolja a jelszavakat a kódban, Ehelyett használja a Azure Key Vault a hitelesítő adatok tárolására és lekérésére.

- [Az Logic Apps-beli adatvédelme és-hozzáférés](logic-apps-securing-a-logic-app.md)

- [Titkos kód beállítása és beolvasása Azure Key Vault](../key-vault/general/quick-create-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

- [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: az Azure-alkalmazás regisztrálása (egyszerű szolgáltatásnév) használatával lekérheti a Recovery Services-TÁROLÓk API-hívásokkal való interakcióhoz használható jogkivonatot.

Számos összekötő azt is megköveteli, hogy először létre kell hoznia egy kapcsolódást a cél szolgáltatáshoz vagy a rendszerhez, és meg kell adnia a hitelesítő adatokat vagy más konfigurációs adatokat, mielőtt a logikai alkalmazásban eseményindítót vagy műveletet alkalmazhat. Az adatok eléréséhez vagy az Ön nevében történő közzétételhez például engedélyeznie kell egy Twitter-fiókhoz való kapcsolódást.

Azure Active Directory (Azure AD) OAuth használó összekötők esetén a kapcsolat létrehozása a szolgáltatásba való bejelentkezést jelenti, például az Office 365, a Salesforce vagy a GitHub szolgáltatást, ahol a hozzáférési token titkosított, és biztonságosan tárolható egy Azure titkos tárolóban. Más összekötők, például az FTP és az SQL esetében olyan kapcsolatra van szükség, amely konfigurációs adatokat tartalmaz, például a kiszolgáló címe, a Felhasználónév és a jelszó. Ezeket a kapcsolati konfigurációs adatokat is titkosítja és biztonságosan tárolja.

- [Az Azure REST API-k meghívása](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Az ügyfélalkalmazás regisztrálása az Azure AD-ben](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Munkafolyamat-eseményindítók API-adatai](/rest/api/logic/workflowtriggers)

- [Az összekötő konfigurációjának ismertetése](../connectors/apis-list.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory (AD) multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatainak követése.

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: az Azure-erőforrások bevezetésére és konfigurálására konfigurált, multi-Factor Authentication (MFA) rendszerjogosultságú hozzáférési munkaállomások használata.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést a Azure Portalhoz az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

Emellett a logikai alkalmazás minden kérelem végpontja közös hozzáférési aláírással (SAS) rendelkezik a végpont URL-címében. Korlátozhatja, hogy a logikai alkalmazás csak bizonyos IP-címekről fogadjon kérelmeket.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Megtudhatja, hogyan korlátozhatja a bejövő IP-címeket Logic Apps](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerként a Azure Logic apps példányokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Ha a Logic Apps támogatja a támogatást, a felügyelt identitással könnyedén hozzáférhet az Azure Active Directory (Azure AD) által védett egyéb erőforrásokhoz, és a hitelesítő adatok és a titkos kódok helyett hitelesítheti az identitását. Az Azure kezeli Ön helyett ezt az identitást, és segít biztonságban tartani az Ön hitelesítő adatait, mivel így nincs szükség titkos kulcsok megadására és rendszeres módosítására.

Az Azure Logic Apps a rendszer és a felhasználó által hozzárendelt felügyelt identitásokat is támogatja. A logikai alkalmazások rendszer vagy felhasználó által hozzárendelt identitást is használhatnak, amely megosztható a logikai alkalmazások egy csoportján belül. Mindkét típusú identitás azonban nem használható. Jelenleg csak bizonyos beépített triggerek és műveletek támogatják a felügyelt identitásokat, a nem felügyelt összekötőket és kapcsolatokat, például:

-  HTTP

-  Azure Functions

-  Azure API Management

-  Azure App Services 

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Az Azure-erőforrásokhoz való hozzáférés hitelesítése felügyelt identitások használatával Azure Logic Apps](create-managed-service-identity.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. 

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerként a Azure Logic apps példányokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Hozzáférése van az Azure AD bejelentkezési tevékenységeihez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik az Azure Sentinel vagy egy harmadik féltől származó SIEM integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics belül is konfigurálhatja a kívánt naplózási riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure ad kockázatkezelési és identitás-védelmi funkcióival konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget. 

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md) 

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: jelenleg nem érhető el; Azure Logic Apps esetében Ügyfélszéf még nem támogatott.

- [Ügyfélszéf által támogatott szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](../security/benchmarks/security-control-data-protection.md)című témakörben talál.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: a "globális", több-bérlős Logic apps szolgáltatásban futó összekötőket a Microsoft telepíti és kezeli. Ezek az összekötők eseményindítókat és műveleteket biztosítanak a Cloud Services, a helyszíni rendszerek vagy mindkettő eléréséhez, beleértve az Office 365, az Azure Blob Storage, a SQL Server, a Dynamics, a Salesforce, a SharePoint és egyéb szolgáltatásokat.

Azon logikai alkalmazások esetében, amelyeknek közvetlen hozzáférésre van szükségük egy Azure-beli virtuális hálózat erőforrásaihoz, létrehozhat egy integrációs szolgáltatási környezetet (ISE), ahol a logikai alkalmazásokat dedikált erőforrásokon hozhatja létre, helyezheti üzembe és futtathatja. Néhány Azure-beli virtuális hálózat privát végpontokat (Azure Private-hivatkozást) használ az Azure-beli (Azure-beli) szolgáltatások, például az Azure Storage, a Azure Cosmos DB vagy a Azure SQL Database, a partner Services vagy az Azure-ban üzemeltetett ügyfélszolgálatok számára. Ha a logikai alkalmazásoknak hozzá kell férniük a privát végpontokat használó virtuális hálózatokhoz, létre kell hoznia, telepítenie és futtatnia kell ezeket a logikai alkalmazásokat egy ISE-ben.

Az ISE létrehozásakor dönthet úgy, hogy belső vagy külső hozzáférési végpontokat használ. A kiválasztott beállítás határozza meg, hogy az ISE-beli Logic apps-beli kérelem vagy webhook-eseményindítók fogadhatnak-e hívásokat a virtuális hálózaton kívülről is.

Emellett különálló előfizetések és felügyeleti csoportok használatával különítse el az elkülönítést az egyes biztonsági tartományokhoz, például a környezeti típusokhoz és az adatérzékeny szintekhez. Korlátozhatja az alkalmazásaihoz és a vállalati környezetekhez igénybe veheti az Azure-erőforrásokhoz való hozzáférés szintjét. Az Azure-erőforrásokhoz való hozzáférést az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével szabályozhatja.

- [Logic Apps-összekötők ismertetése](../connectors/apis-list.md)

- [Hozzáférés az Azure Virtual Network-erőforrásokhoz a Azure Logic Apps integrációs szolgáltatási környezetek (ISEs) használatával](connect-virtual-network-vnet-isolated-environment-overview.md)

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md) 

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md) 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: jelenleg nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Logic Apps számára.

Egy külső gyártótól származó megoldást használhat fel az Azure Marketplace-ről olyan hálózati területekre, amelyek figyelik a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztást küldenek az informatikai szakemberek számára. 

A Microsoft kezeli a Azure Logic Apps alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Azure Logic Apps a logikai alkalmazás futása során az összes adatok titkosítva lettek az átvitel során Transport Layer Security (TLS) és a nyugalmi állapotban. Amikor megtekinti a logikai alkalmazás futási előzményeit, Logic Apps hitelesíti a hozzáférést, majd az egyes futtatásokhoz tartozó kérelmek és válaszok bemeneteit és kimeneteit is tartalmazza. A jelszavakat, titkos kulcsokat vagy más bizalmas adatokat kezelő műveletek esetében azonban meg kell akadályozni, hogy mások megtekintsék és hozzáférjenek az adatokhoz. Ha például a logikai alkalmazás a HTTP-műveletek hitelesítéséhez Azure Key Vault titkos kulcsot kap, akkor a titkos kulcsot el szeretné rejteni a nézetből.

A kérelem-trigger csak Transport Layer Security (TLS) 1,2-t támogat a bejövő kérelmek esetében. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek a TLS 1,2 vagy újabb egyeztetésére. A HTTP-összekötő Transport Layer Security (TLS) 1,0, 1,1 és 1,2 protokollt használó kimenő hívások. 

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

- [Biztonságos hozzáférés és adatAzure Logic Apps – bejövő hívások fogadása a kérelmeken alapuló eseményindítók számára](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Biztonságos hozzáférés és adatforgalom Azure Logic Apps-kimenő hívások más szolgáltatásokhoz és rendszerekhez](logic-apps-securing-a-logic-app.md#secure-outbound-requests)

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Az adatok titkosításának megismerése az Azure-ban](../security/fundamentals/encryption-atrest.md)

- [Ügyfél által felügyelt kulcsok beállítása az integrációs szolgáltatási környezetek (ISEs-EK) Azure Logic Apps-beli inaktív adatok titkosításához](customer-managed-keys-integration-service-environment.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: a Azure Logic apps számos eseményindító és művelet rendelkezik olyan beállításokkal, amelyek lehetővé teszik a bemenetek, a kimenetek vagy mindkettő védelmét a logikai alkalmazás futtatási előzményeiből származó adatok elrejtésével.

A Microsoft kezeli a Azure Logic Apps alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Biztonságos hozzáférés a futtatási előzményekhez](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: csak meghatározott felhasználók vagy csoportok számára engedélyezheti adott feladatok futtatását, például a Logic apps felügyeletét, szerkesztését és megtekintését. Az engedélyeik szabályozásához használja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC), hogy testreszabott vagy beépített szerepköröket rendeljen az Azure-előfizetés tagjaihoz:

- Logic app közreműködő: lehetővé teszi a logikai alkalmazások kezelését, de nem tudja módosítani őket.
- Logikai alkalmazás kezelője: lehetővé teszi a Logic apps olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissítheti őket.

Annak megakadályozása érdekében, hogy mások módosíthassák vagy töröljék a logikai alkalmazást, használhatja az Azure erőforrás-zárolást. Ez a funkció megakadályozza, hogy mások módosíthassák vagy töröljék a termelési erőforrásokat.

- [Biztonságos hozzáférés Azure Logic Apps műveletekhez](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült. A Microsoft kezeli a Azure Logic Apps alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Azure Customer-adatvédelem](../security/fundamentals/protection-customer-data.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Azure Logic apps az Azure Storage-ra támaszkodik az adatok tárolásához és automatikus titkosításához. Ez a titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Alapértelmezés szerint az Azure Storage a Microsoft által felügyelt kulcsokat használja az adatai titkosítására.

Ha integrációs szolgáltatási környezetet (ISE) hoz létre a logikai alkalmazások üzemeltetéséhez, és az Azure Storage által használt titkosítási kulcsok hatékonyabb vezérlését szeretné végezni, akkor a Azure Key Vault használatával beállíthatja, használhatja és kezelheti a saját kulcsát. Ezt a képességet "Bring Your Own Key" (BYOK) néven is nevezik, és a kulcs neve "ügyfél által felügyelt kulcs".

- [Inaktív adatok titkosítása az integrációs szolgáltatási környezetekben Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, ha a módosítások Azure Logic apps, valamint más kritikus vagy kapcsolódó erőforrások esetében is megtörténik.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: nem alkalmazható; A Microsoft a biztonsági rések kezelését a Azure Logic Apps támogató mögöttes rendszereken hajtja végre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; A Microsoft a biztonsági rések kezelését a Azure Logic Apps támogató mögöttes rendszereken hajtja végre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: nem alkalmazható; A Microsoft a biztonsági rések kezelését a Azure Logic Apps támogató mögöttes rendszereken hajtja végre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül.  Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

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

**Útmutató**: az Azure-erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézést, a felügyeleti csoportokat és a különálló előfizetéseket, ahol szükséges. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: hozzon létre egy leltárt a jóváhagyott Azure-erőforrásokról (például összekötők) és a számítási erőforrások jóváhagyott szoftveréről a szervezeti igényeknek megfelelően.

Megjegyzés: a Google adatai és adatvédelmi szabályzatai miatt a Gmail-összekötő csak a Google által jóváhagyott szolgáltatásokkal használható. Ez a helyzet folyamatosan fejlődik, és a jövőben más Google-összekötőket is érinthet.

- [Az összes Logic Apps összekötő listája](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [A Gmail-összekötők problémáinak és korlátainak megismerése](/connectors/gmail/#known-issues-and-limitations)

- [További információ a Google adatvédelmi szabályzatáról](../connectors/connectors-google-data-security-privacy-policy.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára. 

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

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

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az üzleti műveletekhez szükséges Logic Appsekhez kapcsolódó erőforrások, de a szervezet számára nagyobb kockázatot jelenthetnek, a saját virtuális gépén és/vagy virtuális hálózatán belül, és megfelelő védelemmel kell ellátni egy Azure Firewall-vagy hálózati biztonsági csoporttal.

Az üzleti műveletekhez szükséges Logic Apps azonban nagyobb kockázatot jelenthetnek a szervezet számára, ha lehetséges, külön erőforrás-csoportokkal és az Azure RBAC határokkal kell elkülöníteni.

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md) 

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md) 

- [Logic Apps-hozzáférés biztonságossá tétele az Azure RBAC](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Logic apps példányok szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Logic" névtérben egyéni szabályzatok létrehozásához a Logic Apps példányok konfigurációjának naplózásához vagy érvényesítéséhez. Letilthatja például, hogy mások olyan erőforrásokhoz való kapcsolódást hozzanak létre vagy használjanak, amelyeken korlátozni kívánja a hozzáférést.

Emellett a Azure Resource Manager képes a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni annak biztosításához, hogy a konfigurációk megfelelnek vagy meghaladják a szervezete biztonsági követelményeit.

Emellett biztonságos paramétereket is használhat a bizalmas adatok és a titkos kulcsok védelméhez.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Összekötők által létrehozott kapcsolatok blokkolása Azure Logic Apps](block-connections-connectors.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Logic Apps Azure Resource Manager sablonjainak üzembe helyezése](logic-apps-deploy-azure-resource-manager-templates.md)

- [A biztonságos művelet paramétereinek ismertetése](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Paraméterekkel kapcsolatos biztonsági javaslatok](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

Megadhatja és implementálhatja a Azure Logic Apps példányok szabványos biztonsági konfigurációit Azure Policy. Használjon Azure Policy aliasokat a "Microsoft. Logic" névtérben egyéni szabályzatok létrehozásához a Logic Apps példányok konfigurációjának naplózásához vagy érvényesítéséhez. Letilthatja például, hogy mások olyan erőforrásokhoz való kapcsolódást hozzanak létre vagy használjanak, amelyeken korlátozni kívánja a hozzáférést.

Emellett a Azure Resource Manager képes a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni annak biztosításához, hogy a konfigurációk megfelelnek vagy meghaladják a szervezete biztonsági követelményeit.

Továbbá ügyeljen arra, hogy a futtatási előzményekben lévő adatvédelmet a elhomályosítás használatával biztosítsa.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Összekötők által létrehozott kapcsolatok blokkolása Azure Logic Apps](block-connections-connectors.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Logic Apps Azure Resource Manager sablonjainak üzembe helyezése](logic-apps-deploy-azure-resource-manager-templates.md)

- [Biztonságos hozzáférés a futtatási előzményekhez bemenetekhez és kimenetekhez](logic-apps-securing-a-logic-app.md#obfuscate)

- [Biztonságos hozzáférés a paraméterek bemenetéhez](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Paraméterekkel kapcsolatos biztonsági javaslatok](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

Emellett a Azure Resource Manager képes a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni annak biztosításához, hogy a konfigurációk megfelelnek vagy meghaladják a szervezete biztonsági követelményeit.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/index?view=azure-devops)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: beépített Azure Policy definíciók és Azure Policy aliasok használata a "Microsoft. Logic" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Az Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: beépített Azure Policy definíciók és Azure Policy aliasok használata a "Microsoft. Logic" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Az Azure-erőforrások konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés, ha nem létezik] lehetőséget.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a logikai alkalmazások futtatási előzményeinek biztonságos bemeneteit és kimeneteit elhomályosítás használatával teheti meg. Ha különböző környezetekben végez üzembe helyezést, érdemes parameterizing a logikai alkalmazás munkafolyamat-definíciójában lévő, a környezetek alapján változó értékeket. Így elkerülhetők a rögzített adatok egy Azure Resource Manager sablonnal a logikai alkalmazás üzembe helyezéséhez, a bizalmas adatok védelméhez a biztonságos paraméterek meghatározásával, valamint az adatoknak a sablon paraméterei között külön bemenetként való továbbítása a paraméter használatával. A Key Vault segítségével tárolhatja a bizalmas adatokat, és olyan biztonságos sablon-paramétereket használhat, amelyekkel lekérheti ezeket az értékeket Key Vault a telepítés során. Ezután hivatkozhat a Key vaultra és a titkos kulcsokra a paraméter fájljában. 

Ha integrációs szolgáltatási környezetet (ISE) hoz létre a logikai alkalmazások üzemeltetéséhez, és az Azure Storage által használt titkosítási kulcsok hatékonyabb vezérlését szeretné végezni, akkor a Azure Key Vault használatával beállíthatja, használhatja és kezelheti a saját kulcsát. Ezt a képességet "Bring Your Own Key" (BYOK) néven is nevezik, és a kulcs neve "ügyfél által felügyelt kulcs".

- [Biztonságos bemenetek és kimenetek a futtatási előzményekben Azure Logic Apps](logic-apps-securing-a-logic-app.md#obfuscate)

- [Paraméterekkel kapcsolatos biztonsági javaslatok](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Biztonságos hozzáférés a paraméterek bemenetéhez Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Biztonságos paraméterek értékének továbbítása az üzembe helyezés során Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Ügyfél által felügyelt kulcsok beállítása az integrációs szolgáltatási környezetek (ISEs-EK) Azure Logic Apps-beli inaktív adatok titkosításához](customer-managed-keys-integration-service-environment.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a Azure Active Directory (Azure ad) által védett egyéb erőforrások egyszerű elérése és az identitás hitelesítése bejelentkezés nélkül – a logikai alkalmazás a hitelesítő adatok és a titkos kódok helyett felügyelt identitást (korábban Managed Service Identity vagy msi) is használhat. Az Azure kezeli Ön helyett ezt az identitást, és segít biztonságban tartani az Ön hitelesítő adatait, mivel így nincs szükség titkos kulcsok megadására és rendszeres módosítására.

Jelenleg csak bizonyos beépített triggerek és műveletek támogatják a felügyelt identitásokat, a nem felügyelt összekötőket és kapcsolatokat, például:

- HTTP
- Azure Functions
- Azure API Management
- Azure App Services

- [Az Azure-erőforrásokhoz való hozzáférés hitelesítése felügyelt identitások használatával Azure Logic Apps](create-managed-service-identity.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: a logikai alkalmazások futtatási előzményeinek biztonságos bemeneteit és kimeneteit elhomályosítás használatával teheti meg. Ha különböző környezetekben végez üzembe helyezést, érdemes parameterizing a logikai alkalmazás munkafolyamat-definíciójában lévő, a környezetek alapján változó értékeket. Így elkerülhetők a rögzített adatok egy Azure Resource Manager sablonnal a logikai alkalmazás üzembe helyezéséhez, a bizalmas adatok védelméhez a biztonságos paraméterek meghatározásával, valamint az adatoknak a sablon paraméterei között külön bemenetként való továbbítása a paraméter használatával. A Key Vault segítségével tárolhatja a bizalmas adatokat, és olyan biztonságos sablon-paramétereket használhat, amelyekkel lekérheti ezeket az értékeket Key Vault a telepítés során. Ezután hivatkozhat a Key vaultra és a titkos kulcsokra a paraméter fájljában. 

A hitelesítő adatoknak a kódban való azonosítására is lehetőség van a hitelesítőadat-olvasó bevezetésére. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [Biztonságos bemenetek és kimenetek a futtatási előzményekben Azure Logic Apps](logic-apps-securing-a-logic-app.md#obfuscate)

- [Paraméterekkel kapcsolatos biztonsági javaslatok](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Biztonságos hozzáférés a paraméterek bemenetéhez Azure Logic Apps](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Biztonságos paraméterek értékének továbbítása az üzembe helyezés során Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült. A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Logic Apps), azonban az nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Backup), de nem fut a tartalomon. 

A nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage stb. 

A Storage-fiókokba feltöltött kártevők észleléséhez használja a Azure Security Center veszélyforrások észlelését az adatszolgáltatásokhoz. 

- [Ismerje meg a Microsoft kártevő szoftvereket az Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

- [Az adatszolgáltatások fenyegetés-észlelésének megismerése Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: vész-helyreállítási (Dr) megoldás implementálása, amely lehetővé teszi az adatok védelme, a kritikus üzleti funkciókat támogató erőforrások gyors visszaállítását, valamint az üzletmenet folytonosságának fenntartása érdekében folytatott működés fenntartását.

Ez a vész-helyreállítási stratégia arra összpontosít, hogy egy másik helyen állítsa be az elsődleges logikai alkalmazás feladatátvételét egy készenléti vagy biztonsági mentési logikai alkalmazásba, ahol Azure Logic Apps is elérhető. Így ha az elsődlegesen veszteségek, fennakadások vagy meghibásodások merülnek fel, a másodlagos folyamat elvégezheti a munkát. Ehhez a stratégiához az szükséges, hogy a másodlagos logikai alkalmazás és a függő erőforrások már telepítve legyenek, és készen álljanak a másik helyre.

Emellett ki kell bővíteni a logikai alkalmazás mögöttes munkafolyamat-definícióját egy Azure Resource Manager sablonban. Ez a sablon határozza meg az infrastruktúrát, az erőforrásokat, a paramétereket és az egyéb információkat a logikai alkalmazás üzembe helyezéséhez és telepítéséhez.

- [További információ az üzletmenet folytonosságáról és a Azure Logic Apps vész-helyreállításról](business-continuity-disaster-recovery-guidance.md)

- [A Azure Logic Apps üzembe helyezésének automatizálása Azure Resource Manager sablonok használatával](logic-apps-azure-resource-manager-templates-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: vész-helyreállítási (Dr) megoldás implementálása, amely lehetővé teszi az adatok védelme, a kritikus üzleti funkciókat támogató erőforrások gyors visszaállítását, valamint az üzletmenet folytonosságának fenntartása érdekében folytatott működés fenntartását.

Ez a vész-helyreállítási stratégia arra összpontosít, hogy egy másik helyen állítsa be az elsődleges logikai alkalmazás feladatátvételét egy készenléti vagy biztonsági mentési logikai alkalmazásba, ahol Azure Logic Apps is elérhető. Így ha az elsődlegesen veszteségek, fennakadások vagy meghibásodások merülnek fel, a másodlagos folyamat elvégezheti a munkát. Ehhez a stratégiához az szükséges, hogy a másodlagos logikai alkalmazás és a függő erőforrások már telepítve legyenek, és készen álljanak a másik helyre.

Emellett ki kell bővíteni a logikai alkalmazás mögöttes munkafolyamat-definícióját egy Azure Resource Manager sablonban. Ez a sablon határozza meg az infrastruktúrát, az erőforrásokat, a paramétereket és az egyéb információkat a logikai alkalmazás üzembe helyezéséhez és telepítéséhez.

A logikai alkalmazás minden kérelem végpontja közös hozzáférési aláírással (SAS) rendelkezik a végpont URL-címében. Ha Azure Key Vaultt használ a titkok tárolására, gondoskodjon arról, hogy a kulcsok és URL-címek rendszeres automatikus biztonsági mentést készítsenek.

- [További információ az üzletmenet folytonosságáról és a Azure Logic Apps vész-helyreállításról](business-continuity-disaster-recovery-guidance.md)

- [Üzembe helyezés automatizálása Azure Logic Appshez Azure Resource Manager sablonok használatával](logic-apps-azure-resource-manager-templates-overview.md)

- [Biztonságos hozzáférés és adatAzure Logic Apps SAS használatával](logic-apps-securing-a-logic-app.md#sas)

- [Key Vault kulcsok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a vész-helyreállítási stratégiának arra kell összpontosítania, hogy az elsődleges logikai alkalmazást egy másik helyen állítsa be a feladatátvételre egy készenléti vagy biztonsági mentési logikai alkalmazásba, ahol Azure Logic apps is elérhető. Így ha az elsődlegesen veszteségek, fennakadások vagy meghibásodások merülnek fel, a másodlagos folyamat elvégezheti a munkát. Ehhez a stratégiához az szükséges, hogy a másodlagos logikai alkalmazás és a függő erőforrások már telepítve legyenek, és készen álljanak a másik helyre.

Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását. Vegye figyelembe, hogy ez csak az Integration Service Environment (ISE) környezetben futó Logic Appsekre vonatkozik.

- [További információ az üzletmenet folytonosságáról és a Azure Logic Apps vész-helyreállításról](business-continuity-disaster-recovery-guidance.md)

- [Ügyfél által felügyelt kulcsok beállítása az integrációs szolgáltatási környezetek (ISEs-EK) Azure Logic Apps-beli inaktív adatok titkosításához](customer-managed-keys-integration-service-environment.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a vész-helyreállítási stratégiának arra kell összpontosítania, hogy az elsődleges logikai alkalmazást egy másik helyen állítsa be a feladatátvételre egy készenléti vagy biztonsági mentési logikai alkalmazásba, ahol Azure Logic apps is elérhető. Így ha az elsődlegesen veszteségek, fennakadások vagy meghibásodások merülnek fel, a másodlagos folyamat elvégezheti a munkát. Ehhez a stratégiához az szükséges, hogy a másodlagos logikai alkalmazás és a függő erőforrások már telepítve legyenek, és készen álljanak a másik helyre. 

Az ügyfél által felügyelt kulcsok biztonsági mentésének biztosítása. Vegye figyelembe, hogy ez csak az Integration Service Environment (ISE) környezetben futó Logic Appsekre vonatkozik.

A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze Soft-Delete és törölje a védelmet a Key Vaultban.

- [További információ az üzletmenet folytonosságáról és a Azure Logic Apps vész-helyreállításról](business-continuity-disaster-recovery-guidance.md)

- [Ügyfél által felügyelt kulcsok beállítása az integrációs szolgáltatási környezetek (ISEs-EK) Azure Logic Apps-beli inaktív adatok titkosításához](customer-managed-keys-integration-service-environment.md)

- [A Soft-Delete engedélyezése és a védelem kiürítése Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](../security/benchmarks/security-control-incident-response.md)című témakörben talál.*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig. 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [A NIST számítógépes biztonsági incidensek kezelésének útmutatója a saját incidensekre vonatkozó válaszadási terv létrehozásához nyújtott támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt. 

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

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