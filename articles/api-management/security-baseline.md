---
title: Az Azure biztonsági alapkonfigurációja API Management
description: A API Management alapkonfigurációja eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: api-management
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 488fa4738918849759fbefa86ef1e9730b3b0ed0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813487"
---
# <a name="azure-security-baseline-for-api-management"></a>Az Azure biztonsági alapkonfigurációja API Management

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a API Management. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, a biztonsági API Management. **A** API Management nem alkalmazható vezérlők ki vannak zárva.

 
Ha meg API Management az Azure-biztonsági teljesítményteszt teljes leképezését, tekintse meg a teljes API Management alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** API Management Azure-beli virtuális gépek üzembe helyezhetők egy Azure Virtual Network-hálózaton belül, így hozzáférhetnek a hálózaton belüli háttérszolgáltatásokhoz. A fejlesztői portál és API Management konfigurálható úgy, hogy elérhető legyen az internetről (külső) vagy csak a VNeten belül (Belső).

- Külső: a API Management átjáró és a fejlesztői portál elérhető a nyilvános internetről egy külső terheléselosztáson keresztül. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

- Belső: a API Management átjáró és a fejlesztői portál csak a virtuális hálózaton belülről érhető el egy belső terheléselosztáson keresztül. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

Az alhálózatra irányuló bejövő és kimenő forgalom, API Management hálózati biztonsági csoporttal vezérelhető.

- [Az Azure API Management használata virtuális hálózatokkal](api-management-using-with-vnet.md)

- [Az Azure API Management használata belső virtuális hálózattal](api-management-using-with-internal-vnet.md)

- [Az API Management és az Application Gateway integrálása egy belső VNET-en](api-management-howto-integrate-internal-vnet-appgateway.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** A hálózati biztonsági csoportok (NSG-k API Management segítségével szabályozható az alhálózat bejövő és kimenő forgalma. Üzembe helyezhet egy NSG-t a API Management alhálózatán, engedélyezheti az NSG forgalmi naplóit, és naplókat küldhet egy Azure Storage-fiókba a forgalom naplózása érdekében. Az NSG-forgalom naplóit egy Log Analytics-munkaterületre is elküldheti, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő adatforgalmába. A hálózati Traffic Analytics néhány előnye a hálózati tevékenység vizualizációja és a gyorspontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati helytelen konfigurációk azonosítása.

Figyelem: Amikor NSG-t konfigurál a API Management alhálózaton, nyitva kell lennie egy portkészletnek. Ha ezen portok bármelyike nem érhető el, API Management nem működnek megfelelően, és elérhetetlenné válhatnak.

- [Az Azure-beli virtuális biztonsági szolgáltatások NSG-konfigurációinak API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** A kritikus fontosságú webes/HTTP API-k védelméhez konfigurálja a API Management (Vnet) Virtual Network belső módban, és konfigurálja a Azure Application Gateway. Application Gateway egy PaaS-szolgáltatás. Fordított proxyként működik, és L7 terheléselosztást, útválasztást, webalkalmazási tűzfalat (WAF) és egyéb szolgáltatásokat biztosít.

A API Management virtuális hálózatban kiépített virtuális gépek és a Application Gateway kombinálása a következő forgatókönyveket teszi lehetővé:
- Egyetlen forrás API Management az összes API-t a belső és a külső fogyasztók számára egyaránt.
- Egyetlen erőforrás API Management API-k egy részkészletének külső fogyasztók számára való felfedése érdekében.
- A nyilvános internetről a nyilvános internetről API Management a nyilvános internetről való hozzáférés átváltása.

Megjegyzés: Ez a funkció a prémium és a fejlesztői szinten érhető el a API Management.

- [Integráció API Management belső virtuális hálózatba Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [A Azure Application Gateway](../application-gateway/index.yml)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Konfigurálja API Management virtuális Virtual Network (VNet) belüli belső módban, és konfigurálja a Azure Application Gateway. Application Gateway egy PaaS-szolgáltatás. Fordított proxyként működik, és L7 terheléselosztást, útválasztást, webalkalmazási tűzfalat (WAF) és egyéb szolgáltatásokat biztosít.

A API Management virtuális hálózatban kiépített virtuális gépek és a Application Gateway kombinálása a következő forgatókönyveket teszi lehetővé:
- Egyetlen erőforrást API Management, hogy az összes API-t a belső és a külső fogyasztók számára is kihozni.
- Egyetlen forrás API Management api-k egy részkészletének külső fogyasztók számára való felfedése érdekében.
- A nyilvános internetről a nyilvános internetről API Management a hozzáférést.

Megjegyzés: Ez a funkció a prémium és a fejlesztői szinten érhető el a API Management.

Az Azure Security Center intelligencia használatával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel való kommunikációt.

- [Virtuális hálózat API Management integrálása belső virtuális hálózatba Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [A Azure Application Gateway](../application-gateway/index.yml)

- [Az Azure Security Center fenyegetésintelligencia](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** A hálózati biztonsági csoportokkal (NSG API Management keresztül szabályozható az alhálózat bejövő és kimenő forgalma. Telepítsen egy NSG-t API Management alhálózatra, engedélyezze az NSG forgalmi naplóit, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózására. Az NSG-forgalom naplóit egy Log Analytics-munkaterületre is elküldheti, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő adatforgalmába. A hálózati Traffic Analytics a hálózati tevékenységek vizualizációja és a gyors helyek azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati konfigurációk észlelése.

Figyelem: Amikor NSG-t konfigurál a API Management alhálózaton, nyitva kell lennie egy portkészletnek. Ha ezen portok bármelyike nem érhető el, API Management nem működnek megfelelően, és elérhetetlenné válhatnak.

- [Az Azure-beli virtuális biztonsági szolgáltatások NSG-konfigurációinak API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Konfigurálja API Management virtuális Virtual Network (VNet) belüli belső módban, és konfigurálja a Azure Application Gateway. Application Gateway egy PaaS-szolgáltatás. Fordított proxyként működik, és L7 terheléselosztást, útválasztást, webalkalmazási tűzfalat (WAF) és egyéb szolgáltatásokat biztosít. Application Gateway WAF védelmet nyújt a gyakori biztonsági rések és biztonsági rések ellen, és a következő két módban futtatható:
- Észlelési mód: Figyeli és naplózza az összes fenyegetési riasztást. A naplózási diagnosztikát a Diagnosztika Application Gateway kapcsolhatja be. Győződjön meg arról, hogy a WAF-napló ki van jelölve és be van kapcsolva. A webalkalmazási tűzfal nem blokkolja a bejövő kéréseket, ha észlelési módban működik.
- Megelőzési mód: Letiltja a szabályok által észlelt behatolásokat és támadásokat. A támadó egy "403-as jogosulatlan hozzáférés" kivételt kap, és bezárja a kapcsolatot. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

A API Management virtuális hálózatban kiépített virtuális gépek és a Application Gateway kombinálása a következő forgatókönyveket teszi lehetővé:
- Egyetlen forrás API Management az összes API-t a belső és a külső fogyasztók számára egyaránt.
- Egyetlen forrás API Management api-k egy részkészletének külső fogyasztók számára való felfedése érdekében.
- A nyilvános internetről a nyilvános internetről API Management a hozzáférést.

Megjegyzés: Ez a funkció a prémium és a fejlesztői szinten érhető el a API Management.

- [Virtuális hálózat API Management integrálása belső virtuális hálózatba Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [A WAF Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** A webes/HTTP API-kba áramló forgalom kezeléséhez API Management a App Service Environment vagy belső módban társított Virtual Network (VNet) szolgáltatásba.

Belső módban konfigurálja a Azure Application Gateway a API Management. Application Gateway egy PaaS-szolgáltatás. Fordított proxyként működik, és L7 terheléselosztást, útválasztást, webalkalmazási tűzfalat (WAF) és egyéb szolgáltatásokat biztosít. Application Gateway WAF védelmet nyújt a gyakori biztonsági rések és biztonsági rések ellen.

A API Management virtuális hálózatban kiépített virtuális gépek és a Application Gateway kombinálása a következő forgatókönyveket teszi lehetővé:
- Egyetlen erőforrást API Management, hogy az összes API-t a belső és a külső fogyasztók számára is kihozni.
- Egyetlen forrás API Management api-k egy részkészletének külső fogyasztók számára való felfedése érdekében.
- A nyilvános internetről a nyilvános internetről API Management a hozzáférést.

Megjegyzés: Ez a funkció a prémium és a fejlesztői szinten érhető el a API Management.

- [Privát API-k elérhetővéése külső fogyasztók számára](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

- [Virtuális hálózat API Management használata](api-management-using-with-vnet.md)

- [Azure Web Application Firewall a Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [A Azure Application Gateway](../application-gateway/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A Virtual Network (Vnet) szolgáltatáscímkéivel meghatározhatja a hálózati hozzáférés-vezérlést a hálózati biztonsági csoportokon (NSG-k) a API Management alhálózatán. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha megadja a szolgáltatáscímke nevét (például ApiManagement) egy szabály megfelelő forrás- vagy célmezőben, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásával.

Figyelem: Amikor NSG-t konfigurál a API Management alhálózaton, nyitva kell lennie egy portkészletnek. Ha ezen portok bármelyike nem érhető el, API Management nem működnek megfelelően, és elérhetetlenné válhatnak.

- [A szolgáltatáscímkék ismertetése és használata](../virtual-network/service-tags-overview.md)

- [A portokhoz szükséges API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Az Azure-beli virtuális hálózat üzemelő példányaihoz kapcsolódó hálózati beállítások szabványos biztonsági konfigurációit API Management implementálja. A "Microsoft.ApiManagement" és a "Microsoft.Network" névterek Azure Policy-aliasai használatával egyéni szabályzatokat hozhat létre az Azure API Management üzemelő példányai és a kapcsolódó erőforrások hálózati konfigurációjának naplózásához vagy érvényesítéséhez.

Az Azure Blueprints-t is használhatja a nagy méretű Azure-környezetek egyszerűsítéséhez, mivel egyetlen tervdefinícióba kell elhelyeznie az olyan kulcsfontosságú környezeti összetevőket, mint az Azure Resource Manager-sablonok, az Azure szerepköralapú hozzáférés-vezérlése (Azure RBAC) és a szabályzatok. Könnyedén alkalmazhatja a tervet az új előfizetésekhez, környezetekhez, és finomhangolhatja a vezérlést és a felügyeletet a verziószámozás segítségével.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints-terv létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata hálózati biztonsági csoportokhoz (NSG-khez) és a hálózati biztonsággal és forgalommal kapcsolatos egyéb erőforrásokhoz. Az egyes NSG-szabályoknál a "Leírás" mezőben megadhatja az üzleti igényeket és/vagy időtartamot (stb.) a hálózatra/hálózatról be- és kiforgalmat engedélyező szabályokra.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Új Virtual Network](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnaplóval monitorozható a hálózati erőforrások konfigurációja, és észlelhetők az Azure-beli virtuális API Management kapcsolódó hálózati erőforrások változásai. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** A Azure Monitor Log Analytics-munkaterület(ek)et használva lekérdezheti és elvégezheti az elemzéseket, naplókat küldhet az Azure Storage-ba hosszú távú/archiválási vagy offline elemzés céljából, vagy exportálhatja a naplókat más elemzési megoldásokba az Azure-ban és máshol a Azure Event Hubs. Az Azure API Management naplókat és metrikákat ad vissza Azure Monitor adatokhoz. A naplózás részletessége szolgáltatásonként és API-nként konfigurálható.

Az Azure Azure Monitor mellett az Azure API Management integrálható egy vagy több Azure Application Insights szolgáltatásba. A naplózási Application Insights szolgáltatásonként vagy API-nként konfigurálhatóak.

Igény szerint engedélyezheti és be is adatokat Azure Sentinel egy külső biztonsági incidens- és eseménykezeléshez (SIEM).

- [Diagnosztikai beállítások konfigurálása](../azure-monitor/essentials/diagnostic-settings.md#create-in-azure-portal)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Első lépések a Azure Monitor külső SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Egyéni naplózási és elemzési folyamat létrehozása](api-management-howto-log-event-hubs.md)

- [Az Azure Application Insights](api-management-howto-app-insights.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** A vezérlősík naplózásához engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, és küldje el a tevékenységnaplókat egy Log Analytics-munkaterületre jelentéskészítés és elemzés céljából, az Azure Storage-ba hosszú távú biztonság céljából, vagy az Azure Event Hubs-ba más elemzési megoldásokba való exportáláshoz az Azure-ban és máshol. Az Azure-tevékenységnapló adataival meghatározhatja az Azure-beli API Management vezérlősík szintjén végrehajtott írási műveletek (PUT, POST, DELETE) "mit, ki és mikor" műveletét.

Az adatsík naplózásához a diagnosztikai naplók részletes információkat nyújtanak a naplózáshoz és hibaelhárításhoz fontos műveletekkel és hibákkal kapcsolatban. A diagnosztikai naplók különböznek a tevékenységnaplóktól. A tevékenységnaplók az Azure-erőforrásokon végrehajtott műveletekkel kapcsolatos információkat tartalmaznak. A diagnosztikai naplókban az erőforrás által végrehajtott műveletekkel kapcsolatos információk találhatók meg.

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

- [Diagnosztikai beállítások engedélyezése az Azure API Management](./api-management-howto-use-azure-monitor.md#resource-logs)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági naplók tárolásának megőrzésének konfigurálása

**Útmutató:** Azure Monitor log analytics-munkaterület megőrzési időszakát a szervezet megfelelőségi szabályozásának megfelelően állíthatja be. Az Azure Storage-fiókokat hosszú távú/archiválási tárterülethez használhatja.

- [Naplómegőrzési paraméterek beállítása Log Analytics-munkaterületeken](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Naplók archiválása Azure Storage-fiókba](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Az Azure API Management folyamatosan bocsát ki naplókat és metrikákat a Azure Monitor, így közel valós idejű betekintést nyújt az API-k állapotába és állapotába. Az Azure Monitor- és Log Analytics-munkaterület(ök) használatával áttekintheti, lekérdezheti, vizualizálhatja, irányíthatja, archiválhatja, konfigurálhatja a riasztásokat, és műveleteket is végre tud venni a API Management és a kapcsolódó erőforrásokból származó metrikákon és naplókon. Naplók elemzése és figyelése rendellenes viselkedések észlelése és az eredmények rendszeres áttekintése.

Igény szerint integrálhatja a API Management Azure Application Insights, és elsődleges vagy másodlagos monitorozási, nyomkövetési, jelentéskészítési és riasztási eszközként használhatja.

- [Az Azure-beli virtuális szolgáltatások naplóinak figyelése és API Management](./api-management-howto-use-azure-monitor.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [A Log Analytics-munkaterület](../azure-monitor/logs/log-analytics-tutorial.md)

- [Integráció az Azure Application Insights](api-management-howto-app-insights.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, valamint az Azure API Management-példányok diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre. Lekérdezések végrehajtása a Log Analyticsben kifejezések kereséséhez, trendek azonosításához, minták elemzéséhez, valamint az összegyűjtött adatokon alapuló számos további elemzéshez. Riasztásokat a Log Analytics-munkaterület lekérdezései alapján hozhat létre.

Hozzon létre metrikariasztásokat, amelyek figyelmeztetik, ha valami váratlan történik. Értesítéseket kaphat például, ha az Azure API Management-példánya egy adott időszakban túllépte a várt maximális kapacitást, vagy ha bizonyos számú jogosulatlan átjárókérés vagy -hiba történt egy előre meghatározott időszakban.

Igény szerint integrálhatja a API Management Azure Application Insights, és elsődleges vagy másodlagos monitorozási, nyomkövetési, jelentéskészítési és riasztási eszközként használhatja.

Igény szerint engedélyezheti és bevetheti az adatokat Azure Sentinel külső SIEM-hez.

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

- [Diagnosztikai beállítások engedélyezése az Azure API Management](./api-management-howto-use-azure-monitor.md#resource-logs)

- [Riasztási szabály konfigurálása az Azure API Management](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule)

- [Azure API Management-példány kapacitásmetrikák megtekintése](api-management-capacity.md)

- [Az Azure Application Insights](api-management-howto-app-insights.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Az Azure API Management vezérlősíkhoz (Azure Portal) rendszergazdai hozzáféréssel rendelkező fiókok leltárának Azure Portal.

Azure Active Directory (Azure AD) beépített szerepkörökkel rendelkezik, amelyekhez explicit módon kell hozzárendelni és lekérdezhetőnek kell lennie. API Management szerepkörei és szerepkörei Role-Based Access Control lehetővé teszik a hozzáférések finomhangolt kezelését a API Management szolgáltatások és entitások számára.

Emellett a API Management beépített Rendszergazdák csoportot is tartalmaz a API Management felhasználói rendszerében. A API Management az API-k láthatóságát a fejlesztői portálon, és a Rendszergazdák csoport tagjai láthatják az összes API-t.

Kövesse az Azure Security Center felügyeleti fiókok felügyeletével és karbantartásával kapcsolatos ajánlásokban.

- [Szerepköralapú hozzáférés-vezérlés használata az Azure API Managementben](api-management-role-based-access-control.md)

- [Azure-beli virtuálispéldányok felhasználóinak listájának API Management le](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Címtárbeli szerepkörhöz rendelt felhasználók listájának lekért listája az Azure AD-ban a PowerShell használatával](/powershell/module/az.resources/get-azroleassignment)

- [Címtárszerepk szerepkör-definíció lekért használata az Azure AD-ban a PowerShell használatával](/powershell/module/az.resources/get-azroledefinition)

- [Az identitással és hozzáféréssel kapcsolatos javaslatok Azure Security Center](../security-center/recommendations-reference.md#identityandaccess-recommendations)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** az Azure API Management nem rendelkezik az alapértelmezett jelszavak/kulcsok fogalmával.

Az Azure API Management előfizetések, amelyek az API-khoz való hozzáférés biztonságossá tétele egyik eszközét jelentik, azonban a létrehozott előfizetési kulcspárokkal is rendelkezik. Az ügyfelek bármikor újragenerálhatja ezeket az előfizetői kulcsokat.

- [Az Azure API Management-előfizetések ismertetése](api-management-subscriptions.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban. Az Azure Security Center és hozzáférés-kezelés használatával figyelheti a rendszergazdai fiókok számát.

Emellett a dedikált rendszergazdai fiókok nyomon követéséhez használhatja a Azure Security Center vagy beépített Azure-szabályzatok javaslatait, például:
- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

- [A Azure Security Center identitás és hozzáférés figyelése (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Az Azure API Management konfigurálható úgy, hogy az Azure Active Directory (Azure AD) identitásszolgáltatóként használva hitelesítse a felhasználókat a fejlesztői portálon, hogy kihasználja az Azure AD által kínált SSO-képességeket. A konfigurálás után az új fejlesztői portál felhasználói dönthetnek úgy, hogy követik a használatra való regisztráció folyamatát. Először hitelesítsék magukat az Azure AD-ban, majd a hitelesítés után befejezik a regisztrációt a portálon.

- [Fejlesztői fiókok jogosultsága az Azure AD használatával az Azure API Management](api-management-howto-aad.md)

Másik lehetőségként a bejelentkezési/bejelentkezési folyamat tovább testreszabható delegálás segítségével. A delegálás lehetővé teszi, hogy meglévő webhelyét használja a fejlesztői bejelentkezéshez/regisztrációhoz és a termékekre való előfizetéshez, nem pedig a fejlesztői portál beépített funkcióinak használatával. Lehetővé teszi, hogy a webhely tulajdonában áll a felhasználói adatoknak, és egyéni módon végezze el a lépések érvényesítését.

- [Felhasználói regisztráció és termék-előfizetés delegálása](api-management-howto-setup-delegation.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center identitás- és hozzáférés-kezelésre vonatkozó javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomások (PAW) használata az Azure-erőforrásokba való bejelentkezéshez és konfiguráláshoz konfigurált többtényezős hitelesítéssel.

- [Tudnivalók a Privileged Access munkaállomásról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
  
- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával naplókat és riasztásokat generál, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

Emellett az Azure AD kockázatészlelései segítségével megtekintheti a kockázatos felhasználói viselkedéssel kapcsolatos riasztásokat és jelentéseket.

- [A Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázatészlelései](../active-directory/identity-protection/overview-identity-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés elnevezett helyeinek használatával hozzáférést engedélyez a Azure Portal ip-címtartományok vagy országok/régiók meghatározott logikai csoportosításaiból.

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** Amikor csak lehetséges, használja Azure Active Directory (Azure AD) központi hitelesítési és engedélyezési rendszerként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt. Az Azure AD emellett a felhasználói hitelesítő adatokat is megsóz, hashekkel és biztonságosan tárolja.

Konfigurálja az Azure API Management fejlesztői portált a fejlesztői fiókok Hitelesítéséhez az Azure AD használatával.

Konfigurálja az Azure API Management-példányt, hogy az OAuth 2.0 protokoll és az Azure AD használatával védje az API-kat.

- [Fejlesztői fiókok jogosultsága az Azure AD használatával az Azure API Management](api-management-howto-aad.md)

- [API-k védelme az OAuth 2.0 És az Azure AD és a API Management](api-management-howto-protect-backend-with-aad.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Az ügyfelek az Azure Identity Access Reviews használatával hatékonyan kezelhetik a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható annak biztosítása érdekében, hogy csak a megfelelő felhasználók férnek hozzá.

Az ügyfelek leltárt API Management felhasználói fiókokról, és szükség szerint egyeztetik a hozzáférést. A API Management a fejlesztők azok az API-k, amelyek a API Management. Alapértelmezés szerint az újonnan létrehozott fejlesztői fiókok aktívak, és a Fejlesztők csoporthoz vannak társítva. Az aktív állapotban lévő fejlesztői fiókok az összes olyan API elérésére használhatók, amelyekhez előfizetéssel rendelkezik.

A rendszergazdák létrehozhatnak egyéni csoportokat, vagy külső csoportokat használhatnak a társított Azure AD-bérlőkben. A fejlesztők mellett az egyéni és külső csoportoknak is lehet adni láthatóságot és hozzáférést az API-termékekhez.

- [Felhasználói fiókok kezelése az Azure API Managementben](api-management-howto-create-or-invite-developers.md)

- [A felhasználók listájának API Management le](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Csoportok létrehozása és használata fejlesztői fiókok kezeléséhez az Azure API Managementben](api-management-howto-create-groups.md)

- [Az Azure Identity Hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Az Azure API Management-példány konfigurálása a fejlesztői fiókok hitelesítéséhez az Azure Active Directory (Azure AD) identitásszolgáltatóként való használatával az Azure API Management.

Konfigurálja az Azure API Management-példányt, hogy az OAuth 2.0 protokoll és az Azure AD használatával védje az API-kat.

Konfigurálja a JWT érvényesítési szabályzatát a bejövő API-kérések számára egy érvényes jogkivonat meglétének és érvényességének kényszerítése érdekében.

Diagnosztikai beállításokat hozhat létre az Azure AD felhasználói fiókokhoz, és elküldhet auditnaplókat és bejelentkezési naplókat egy Log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat a Log Analyticsben. Emellett a Log Analytics-munkaterületet is Azure Sentinel külső SIEM-hez.

Konfigurálja a speciális monitorozást API Management házirend használatával, rögzítse a biztonsági elemzéshez szükséges további környezeti információkat, és küldje el Azure Sentinel vagy külső `log-to-eventhub` SIEM-nek.

- [Fejlesztői fiókok jogosultsága az Azure AD használatával az Azure API Management](api-management-howto-aad.md)

- [API-k védelme az OAuth 2.0 És az Azure AD és a API Management](api-management-howto-protect-backend-with-aad.md)

- [API Management hozzáférés-korlátozási szabályzatok](api-management-access-restriction-policies.md)

- [Azure AD-naplók integrálása Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Az Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Az API-k speciális monitorozása](api-management-log-to-eventhub-sample.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** A fiók bejelentkezési viselkedésének a vezérlősíkon (Azure Portal) való eltéréséhez használja az Azure Active Directory (Azure AD) Identity Protection és kockázatészlelési funkciókat a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszok konfigurálához. Az adatokat be is Azure Sentinel vizsgálathoz.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés biztosítanak a Microsoftnak a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** Jelenleg nem érhető el; Ügyfélszéf Azure-erőforrások jelenleg nem támogatottak API Management.

- [A Ügyfélszéf szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrásokat.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Különálló előfizetéseket és/vagy felügyeleti csoportokat implementálja a fejlesztéshez, teszteléshez és éles környezethez. Az Azure API Management-példányokat virtuális hálózattal (VNet)/alhálózattal kell elválasztani, és megfelelően meg kell címkézni.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Az Azure API Management használata virtuális hálózatokkal](api-management-using-with-vnet.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** Jelenleg nem érhető el; az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók jelenleg nem érhetők el az Azure API Management.

A Microsoft felügyeli az Azure API Management mögöttes infrastruktúráját, és szigorú szabályozásokat valósított meg az ügyféladatok elvesztésének vagy kitettségének megakadályozására.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** A felügyeleti sík hívásait TLS Azure Resource Manager keresztül lehet kezdeményezni. Érvényes JSON webes jogkivonatra (JWT) van szükség. Az adatsík-hívások TLS-sel és az egyik támogatott hitelesítési mechanizmussal (például ügyfél-tanúsítvánnyal vagy JWT-vel) védhetőek.

- [Az Azure-beli adatvédelem API Management](#data-protection)

- [TLS-beállítások kezelése az Azure API Management](api-management-howto-manage-protocols-ciphers.md)

- [API-k védelme az Azure API Management Azure Active Directory (Azure AD) használatával](api-management-howto-protect-backend-with-aad.md)

- [Api-k védelme az Azure API Management Azure AD B2C](howto-protect-backend-frontend-azure-ad-b2c.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosításához

**Útmutató:** Még nem érhető el; az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el az Azure API Management. Címkézheti API Management olyan Azure-szolgáltatásokat, amelyek bizalmas adatokat feldolgozhatnak, és ha megfelelőségi célból szükség van rá, külső féltől származó megoldást valósítanak meg.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft minden ügyféltartalmat bizalmasként kezel, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és -kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Erőforrásokhoz való hozzáférés vezérlése az Azure RBAC használatával 

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az Azure-erőforrásokhoz való hozzáférés API Management. Az Azure API Management szerepköralapú hozzáférés-vezérlésre támaszkodik, hogy lehetővé tegye a API Management-szolgáltatások és entitások (például API-k és szabályzatok) hozzáférés-kezelését.

- [Az Azure szerepköralapú hozzáférés-vezérlésének használata az Azure API Management](api-management-role-based-access-control.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz való.

A Microsoft felügyeli az Azure API Management mögöttes infrastruktúráját, és szigorú vezérlőket valósított meg az ügyféladatok elvesztésének vagy kitettségének megakadályozására.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Monitor az Azure-tevékenységnaplóval használva riasztásokat hozhat létre arra vonatkozóan, hogy mikor történik változás az éles környezetben Azure Functions alkalmazásokban, valamint más kritikus vagy kapcsolódó erőforrásokban.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

- [A Azure Monitor Azure-tevékenységnapló használata az Azure API Management](api-management-howto-use-azure-monitor.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált biztonsági rések ellenőrzési eszközeinek futtatása

**Útmutató:** Jelenleg nem érhető el; biztonsági rések felmérése Azure Security Center jelenleg nem érhető el az Azure API Management.

A Microsoft által ellenőrzött és javított mögöttes platform. Tekintse át az elérhető biztonsági vezérlőket a szolgáltatáskonfigurációval kapcsolatos biztonsági rések csökkentése érdekében.

- [Az Azure-ban elérhető biztonsági vezérlők API Management]()

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának üzembe helyezése

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Automatikus javításkezelési megoldás üzembe helyezése külső szoftvercímek számára

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: A biztonsági rések biztonsági vizsgálatának összehasonlítása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Jelenleg nem érhető el; A biztonsági rések felmérése Azure Security Center azure-beli virtuális API Management.

A Microsoft által beolvasott és javított mögöttes platform. Az ügyfél áttekinti a számukra elérhető biztonsági vezérlőket a szolgáltatás konfigurációjának biztonsági rései számának csökkentése érdekében.


**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** Azure Resource Graph használatával lekérdezheti/felderítheti az előfizetés(ek)beli összes erőforrást (pl. számítás, tárolás, hálózat, portok és protokollok stb.). Győződjön meg arról, hogy megfelelő (olvasási) engedélyeket használ a bérlőben, és számba veszi az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

Bár a klasszikus Azure-erőforrások a Resource Graph felderíthetőek, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása olyan Azure-erőforrásokra, amelyek metaadatokat adva logikailag rendszerezik őket egy taxonóniában.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az Azure-erőforrások rendszerezéséhez és nyomon követéséhez címkézést, felügyeleti csoportokat és különálló előfizetéseket használhat, ha szükséges. Rendszeresen egyeztetni kell a leltárt, és gondoskodni kell arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

Emellett az Azure Policy használatával korlátozásokat is érvényben hagyhat az ügyfél-előfizetés(ök)ben az alábbi beépített szabályzatdefiníciók használatával létrehozott erőforrások típusára:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és nyilvántartása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz való.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy a következő beépített szabályzatdefiníciók használatával az előfizetés(ök)ben létrehozható erőforrások típusának korlátozására:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

A Azure Resource Graph lekérdezéséhez/felderítéséhez használhatja az előfizetése(ek)et. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazások használata

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetés(ök)ben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: A jóváhagyott szoftvercímek leltárának fenntartása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés konfigurálásával korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

- [Szerepköralapú hozzáférés-vezérlés az Azure API Management](api-management-role-based-access-control.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: A felhasználók számítási erőforrásokon belüli szkriptek végrehajtására vonatkozó képességének korlátozása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz való.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: A magas kockázatú alkalmazások fizikai vagy logikai elkülönítése

**Útmutató:** Nem alkalmazható; Ez a javaslat a számítási erőforrásokon futó webalkalmazások Azure App Service szánt.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Standard biztonsági konfigurációkat definiálhat és valósíthat meg az Azure API Management szolgáltatáshoz a Azure Policy. A Azure Policy a "Microsoft.ApiManagement" névtérben létrehozott aliasokkal egyéni szabályzatokat hozhat létre az Azure-beli virtuális szolgáltatások konfigurációjának API Management érvényesítéséhez.

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz való.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** Standard biztonsági konfigurációkat határozhat meg és valósíthat meg az Azure API Management szolgáltatásaihoz a Azure Policy. Használjon Azure Policy aliasokat a "Microsoft.ApiManagement" névtérben egyéni szabályzatok létrehozásához az Azure-beli virtuális API Management naplózásához API Management kikényszeríteni. Az Azure Policy [deny] és a [deploy if not exist] ([Üzembe helyezés, ha nem létezik]) használatával kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Ha egyéni Azure-szabályzatdefiníciókat használ, az Azure DevOps vagy az Azure Repos használatával biztonságosan tárolhatja és kezelheti az Azure API Management szolgáltatás konfigurációját.

- [Fájlok tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Standard biztonsági konfigurációkat határozhat meg és valósíthat meg az Azure API Management szolgáltatásaihoz a Azure Policy. Használjon Azure Policy aliasokat a "Microsoft.ApiManagement" névtérben egyéni szabályzatok létrehozásához az Azure-beli virtuális API Management naplózásához API Management kikényszeríteni. Az Azure Policy [deny] és a [deploy if not exist] ([Üzembe helyezés, ha nem létezik]) használatával kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Konfigurációkezelő eszközök központi telepítése operációs rendszerekhez

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz való.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** Az Azure API Management DevOps Resource Kit használatával végezhet konfigurációkezelést az Azure API Management.

Emellett standard biztonsági konfigurációkat határozhat meg és valósíthat meg az Azure API Management szolgáltatásokhoz a Azure Policy. A Azure Policy a "Microsoft.ApiManagement" névtérben létrehozott aliasokkal egyéni szabályzatokat hozhat létre az Azure-beli virtuális API Management naplózásához vagy kikényszerításához. Az Azure Policy [megtagadása] és [üzembe helyezés, ha nem létezik] használatával kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Automatikus konfigurációfigyelés megvalósítása operációs rendszerekhez

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz való.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** A Azure Active Directory (Azure AD) által létrehozott felügyeltszolgáltatás-identitás használatával lehetővé teheti, hogy API Management-példány könnyedén és biztonságosan hozzáférjen más Azure AD-védelem alatt álló erőforrásokhoz, például Azure Key Vault.

- [Felügyelt identitás létrehozása egy felügyelt API Management példányhoz](api-management-howto-use-managed-service-identity.md)

- [Szabályzat a felügyelt identitással való hitelesítéshez](./api-management-authentication-policies.md#ManagedIdentity)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódban. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevővédelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Központilag felügyelt kártevőirtó szoftverek használata

**Útmutató:** Nem alkalmazható; Ez a javaslat számítási erőforrásokhoz ajánlott.

A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure API Management), de nem fut az ügyféltartalmakon.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** Nem alkalmazható; Ez a javaslat adatok tárolására tervezett, nem számítási erőforrásokhoz készült.

A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure API Management), de nem fut az ügyféltartalmakon.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: A kártevőirtó szoftverek és aláírások frissítésének biztosítása

**Útmutató:** Nem alkalmazható; Ez a javaslat adatok tárolására tervezett, nem számítási erőforrásokhoz készült.

A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure API Management), de nem fut az ügyféltartalmakon.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** Az API-k Azure API Management-n keresztül történő közzétételével és kezelésével kihasználhatja azokat a hibatűrési és infrastruktúra-képességeket, amelyek más esetben manuálisan tervezhetők, valósíthatóak meg és kezelhetők. API Management támogatja a többrétű üzembe helyezést, ami az adatsíkot a regionális meghibásodások szempontjából is megszemélyesítővé teszi anélkül, hogy üzemeltetési többletterhelést okoz.

A szolgáltatások biztonsági mentési és visszaállítási funkciói API Management biztosítják a vészhelyreállítási stratégia megvalósításához szükséges építőelemeket. A biztonsági mentési és visszaállítási műveletek manuálisan vagy automatikusan is elvégezhetők.

- [Adatsík API Management régiókba történő üzembe helyezése](api-management-howto-deploy-multi-region.md)

- [Vészhelyreállítás a biztonsági mentés és visszaállítás használatával az Azure API Managementben](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [A biztonsági mentési művelet API Management hívása](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

- [A visszaállítási művelet API Management hívása](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és ügyfél által kezelt kulcsok biztonsági mentése

**Útmutató:** Az Azure által biztosított biztonsági mentési és visszaállítási műveletek API Management biztonsági mentést és visszaállítást hajt végre a rendszerről.

A felügyelt identitások segítségével tanúsítványokat szerezhet be a Azure Key Vault egyéni API Management számára. Biztonsági másolat készítése a biztonsági másolatban tárolt Azure Key Vault.

- [Vészhelyreállítás a biztonsági mentés és visszaállítás használatával az Azure API Managementben](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Biztonsági másolat készítése Azure Key Vault tanúsítványokról](/powershell/module/az.keyvault/backup-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** A biztonsági másolatok ellenőrzéséhez végezze el a szolgáltatás és a tanúsítványok biztonsági másolatból való visszaállításának tesztelését.

- [A visszaállítási művelet API Management hívása](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

- [Az új tanúsítványok Azure Key Vault visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Az Azure API Management a biztonsági másolatokat az ügyfél tulajdonában lévő Azure Storage-fiókokba írja. A biztonsági mentés védelméhez kövesse az Azure Storage biztonsági javaslatait.

- [Vészhelyreállítás a biztonsági mentés és visszaállítás használatával az Azure API Managementben](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Biztonsági javaslatok a Blob Storage-hez](../storage/blobs/security-recommendations.md)

Engedélyezze Soft-Delete a Key Vault, hogy megvédje a kulcsokat a véletlen vagy rosszindulatú törléstől.

- [Az Soft-Delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Útmutatás saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Az NIST számítógépes biztonsági incidenskezelési útmutatójának segítségével segítséget nyújt saját incidenskezelési tervének létrehozásában](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így fontossági sorrendbe adhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, Security Center mennyire biztos a riasztás kiadásához használt eredményben vagy elemzésben, valamint hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

Emellett egyértelműen jelöljük meg az előfizetéseket (például: éles környezetben, nem éles környezetben) címkéket használ, és létrehoz egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatokat feldolgozók számára. Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [NIST-kiadvány – Útmutató az IT-tervekhez és -képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidensek kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy illetéktelen vagy jogosulatlan fél fért hozzá adataihoz. Tekintse át az incidenseket a tény után, és győződjön meg arról, hogy a problémák megoldódnak.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A riasztások Azure Security Center és javaslatokat exportálhatja a Continuous Export (Folyamatos exportálás) funkcióval az Azure-erőforrásokat érintő kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A munkafolyamat-automatizálási szolgáltatás Azure Security Center a biztonsági riasztások és javaslatok "automatikus aktiválásával" Logic Apps" keresztül.

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft kötelezettségvállalási szabályait annak érdekében, hogy a behatolási tesztek ne sértsen meg Microsoft-szabályzatokat: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [A Microsoft red teaming stratégiájáról és végrehajtásáról, valamint a Microsoft által felügyelt felhőinfra infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett élő webhely-behatolási tesztekről itt talál további információt.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
