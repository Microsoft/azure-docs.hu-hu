---
title: API Management Azure biztonsági alapterve
description: API Management Azure biztonsági alapterve
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6f82114874c9e85e44df6684d0b0cf624903a50f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582521"
---
# <a name="azure-security-baseline-for-api-management"></a>API Management Azure biztonsági alapterve

Az API Management Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: az [Azure biztonsági alaptervek áttekintése](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: az Azure API Management üzembe helyezhető egy azure-Virtual Network (vnet) belül, így a hálózaton belül elérhetővé válik a háttér-szolgáltatások. A fejlesztői portál és API Management átjáró úgy konfigurálható, hogy az internetről (külső) vagy csak a vnet (belső) belülről legyen elérhető.
- Külső: a API Management átjáró és a fejlesztői portál a nyilvános internetről külső terheléselosztó használatával érhető el. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.
- Belső: a API Management átjáró és a fejlesztői portál csak a virtuális hálózaton belülről érhető el belső terheléselosztó használatával. Az átjáró hozzáférhet a virtuális hálózaton belüli erőforrásokhoz.

A bejövő és a kimenő forgalom abba az alhálózatba, amelyben a API Management telepítve van, a hálózati biztonsági csoport használatával vezérelhető.

* [Az Azure API Management használata virtuális hálózatokkal](./api-management-using-with-vnet.md)

* [Az Azure API Management használata belső virtuális hálózattal](./api-management-using-with-internal-vnet.md)

* [Az API Management és az Application Gateway integrálása egy belső VNET-en](./api-management-howto-integrate-internal-vnet-appgateway.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a bejövő és a kimenő forgalom abba az alhálózatba, amelyben a API Management központi telepítése felügyelhető hálózati biztonsági csoportok (NSG-EK) használatával. Helyezzen üzembe egy NSG az API Management alhálózaton, és engedélyezze a NSG flow-naplókat, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Vigyázat: Ha a API Management alhálózaton NSG konfigurál, a megnyitható portok halmaza szükséges. Ha a portok bármelyike nem érhető el, előfordulhat, hogy API Management nem működik megfelelően, és elérhetetlenné válhat.

* [Az Azure API Management NSG-konfigurációinak megismerése](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: a kritikus webes/http API-k a Virtual Network (vnet) belső módban való API Management konfigurálásához és egy Azure-Application Gateway konfigurálásához. Application Gateway egy Pásti szolgáltatás. Fordított proxyként működik, és biztosítja a L7 terheléselosztást, az útválasztást, a webalkalmazási tűzfalat (WAF) és más szolgáltatásokat.

A belső vnet API Management kiépített, a Application Gateway előtérrel való egyesítése a következő helyzetekben teszi lehetővé:
- Egyetlen API Management erőforrást használhat az összes API-nak a belső fogyasztók és a külső fogyasztók számára való kimutatása érdekében.
- Használjon egy API Management erőforrást az API-k külső felhasználóknak való kimutatása érdekében.
- A nyilvános internetről be-és kikapcsolt API Managementhoz való hozzáférés váltásának módja.

Megjegyzés: Ez a funkció a API Management prémium és fejlesztői szintjein érhető el.

* [API Management integrálása belső VNET a Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Az Azure Application Gateway ismertetése](../application-gateway/index.yml)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: API Management konfigurálása egy Virtual Network (vnet) belül belső módban, valamint egy Azure-Application Gateway konfigurálása. Application Gateway egy Pásti szolgáltatás. Fordított proxyként működik, és biztosítja a L7 terheléselosztást, az útválasztást, a webalkalmazási tűzfalat (WAF) és más szolgáltatásokat.

A belső vnet API Management kiépített, a Application Gateway előtérrel való egyesítése a következő helyzetekben teszi lehetővé:
- Egyetlen API Management erőforrást használhat az összes API-nak a belső fogyasztók és a külső fogyasztók számára való kimutatása érdekében.
- Használjon egy API Management erőforrást az API-k külső felhasználóknak való kimutatása érdekében.
- A nyilvános internetről be-és kikapcsolt API Managementhoz való hozzáférés váltásának módja.

Megjegyzés: Ez a funkció a API Management prémium és fejlesztői szintjein érhető el.

A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

* [API Management integrálása belső VNET a Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Az Azure Application Gateway ismertetése](../application-gateway/index.yml)

* [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: a bejövő és a kimenő forgalom abba az alhálózatba, amelyben a API Management központi telepítése felügyelhető hálózati biztonsági csoportok (NSG-EK) használatával. Helyezzen üzembe egy NSG az API Management alhálózaton, és engedélyezze a NSG flow-naplókat, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Vigyázat: Ha a API Management alhálózaton NSG konfigurál, a megnyitható portok halmaza szükséges. Ha a portok bármelyike nem érhető el, előfordulhat, hogy API Management nem működik megfelelően, és elérhetetlenné válhat.

* [Az Azure API Management NSG-konfigurációinak megismerése](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: API Management konfigurálása egy Virtual Network (vnet) belül belső módban, valamint egy Azure-Application Gateway konfigurálása. Application Gateway egy Pásti szolgáltatás. Fordított proxyként működik, és biztosítja a L7 terheléselosztást, az útválasztást, a webalkalmazási tűzfalat (WAF) és más szolgáltatásokat. Application Gateway a WAF a gyakori biztonsági rések és sebezhetőségek elleni védelmet nyújt, és a következő két módban futhat:
- Észlelési mód: figyeli és naplózza az összes veszélyforrás riasztást. A diagnosztika szakaszban bekapcsolhatja Application Gateway naplózási diagnosztikát. Győződjön meg arról, hogy a WAF-napló ki van választva és be van kapcsolva. A webalkalmazási tűzfal nem blokkolja a bejövő kérelmeket, ha az észlelési módban működik.
- Megelőzési mód: a szabályok által észlelhető behatolások és támadások blokkolása. A támadó "403 jogosulatlan hozzáférés" kivételt kap, és a kapcsolat bezárult. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

A belső vnet API Management kiépített, a Application Gateway előtérrel való egyesítése a következő helyzetekben teszi lehetővé:
- Egyetlen API Management erőforrást használhat az összes API-nak a belső fogyasztók és a külső fogyasztók számára való kimutatása érdekében.
- Használjon egy API Management erőforrást az API-k külső felhasználóknak való kimutatása érdekében.
- A nyilvános internetről be-és kikapcsolt API Managementhoz való hozzáférés váltásának módja.

Megjegyzés: Ez a funkció a API Management prémium és fejlesztői szintjein érhető el.

* [API Management integrálása belső VNET a Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Az Azure Application Gateway WAF ismertetése](../web-application-firewall/ag/ag-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: a webes/http API-khoz áramló forgalom kezeléséhez API Managementt kell telepíteni a külső vagy belső módban app Service Environmenthoz társított Virtual Network (vnet).

Belső módban a API Management előtt állítson be egy Azure-Application Gateway. Application Gateway egy Pásti szolgáltatás. Fordított proxyként működik, és biztosítja a L7 terheléselosztást, az útválasztást, a webalkalmazási tűzfalat (WAF) és más szolgáltatásokat. Application Gateway WAF védelmet biztosít a gyakori biztonsági rések és sebezhetőségek ellen.

A belső vnet API Management kiépített, a Application Gateway előtérrel való egyesítése a következő helyzetekben teszi lehetővé:
- Egyetlen API Management erőforrást használhat az összes API-nak a belső fogyasztók és a külső fogyasztók számára való kimutatása érdekében.
- Használjon egy API Management erőforrást az API-k külső felhasználóknak való kimutatása érdekében.
- A nyilvános internetről be-és kikapcsolt API Managementhoz való hozzáférés váltásának módja.

Megjegyzés: Ez a funkció a API Management prémium és fejlesztői szintjein érhető el.

* [Privát API-k közzététele külső felhasználóknak](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [API Management használata a vnet belül](./api-management-using-with-vnet.md)

* [Azure webalkalmazási tűzfal az Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

* [Az Azure Application Gateway ismertetése](../application-gateway/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Virtual Network (vnet) szolgáltatás-címkék használatával definiálhatja a hálózati biztonsági csoportok (nsg) API Management alhálózatokon használt hálózati hozzáférés-vezérléseit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Vigyázat: Ha a API Management alhálózaton NSG konfigurál, a megnyitható portok halmaza szükséges. Ha a portok bármelyike nem érhető el, előfordulhat, hogy API Management nem működik megfelelően, és elérhetetlenné válhat.

* [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

* [A API Managementhoz szükséges portok](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure API Management üzemelő példányokhoz kapcsolódó hálózati beállítások szabványos biztonsági konfigurációinak meghatározása és implementálása. Használjon Azure Policy aliasokat a "Microsoft. ApiManagement" és a "Microsoft. Network" névterekben, hogy egyéni házirendeket hozzon létre az Azure API Management üzemelő példányok és a kapcsolódó erőforrások hálózati konfigurációjának naplózásához vagy érvényesítéséhez. 

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagy léptékű Azure-környezeteket a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek egyetlen terv szerinti definícióban való kicsomagolásával. A tervet egyszerűen alkalmazhatja új előfizetésekre, környezetekre, valamint a verziószámozáson keresztül történő finomhangolásra és felügyeletre.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használja a hálózati biztonsági csoportokhoz (NSG) és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz tartozó címkéket. Az egyes NSG-szabályok esetében a "Leírás" mező használatával megadhatja az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

* [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti a hálózati erőforrás-konfigurációkat, és felderítheti az Azure API Management üzemelő példányokhoz társított hálózati erőforrások változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

* [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-API Management időforrásait.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a Azure monitoron belül log Analytics munkaterülettel kérdezheti le és hajthatja végre az elemzéseket, naplókat küldhet az Azure Storage-ba hosszú távú/archiválási vagy offline elemzés céljából, vagy exportálhatja a naplókat más elemzési megoldásba az Azure-ban és más azure-Event Hubs használatával. Az Azure API Management alapértelmezés szerint Azure Monitor a naplókat és a metrikákat. A naplózás részletessége a szolgáltatásra és API-ra is konfigurálható.

A Azure Monitoron kívül az Azure API Management egy vagy több Azure Application Insights-szolgáltatással is integrálható. Application Insights naplózási beállításai konfigurálható szolgáltatásként vagy API-alapon.

Opcionálisan, engedélyezheti és elküldheti az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események kezelése (SIEM) szolgáltatásait.

* [Diagnosztikai beállítások konfigurálása](../azure-monitor/essentials/diagnostic-settings.md#create-in-azure-portal)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Egyéni naplózási és elemzési folyamat létrehozása](./api-management-howto-log-event-hubs.md)

* [Az Azure Application Insights integrációja](./api-management-howto-app-insights.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a vezérlési síkok naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a tevékenységek naplóit egy log Analytics munkaterületre jelentéskészítés és elemzés céljából, az Azure Storage-ba a hosszú távú megőrzés érdekében Event Hubs, az Azure-ba való exportáláshoz az Azure-ban más elemzési megoldásokban, illetve máshol. Az Azure-tevékenység naplójának adatai segítségével meghatározhatja a "mit, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az Azure API Management szolgáltatás vezérlési síkja szintjén.

Az adatsík naplózási naplózásához a diagnosztikai naplók részletes információkat biztosítanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. A diagnosztikai naplók különböznek a tevékenységnaplóktól. A tevékenységnaplók az Azure-erőforrásokon végrehajtott műveletekkel kapcsolatos információkat tartalmaznak. A diagnosztikai naplókban az erőforrás által végrehajtott műveletekkel kapcsolatos információk találhatók meg.

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

* [Az Azure API Management diagnosztikai beállításainak engedélyezése](./api-management-howto-use-azure-monitor.md#activity-logs)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

* [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

* [Naplók archiválása Azure Storage-fiókba](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure API Management folyamatosan kibocsátja a naplókat és mérőszámokat a Azure monitorba, így közel valós idejű láthatóságot biztosít az API-k állapotáról és állapotáról. A Azure Monitor és Log Analytics munkaterület (ek) segítségével áttekintheti, lekérdezheti, megjelenítheti, átirányíthatja és archiválhatja a riasztásokat, valamint műveleteket végezhet a API Management és a kapcsolódó erőforrásokból származó mérőszámokon és naplókon. Elemezheti és figyelheti a rendellenes viselkedések naplóit, és rendszeresen áttekintheti az eredményeket.

Opcionálisan integrálhatja API Management az Azure Application Insights-nal, és használhatja elsődleges vagy másodlagos figyelési, nyomkövetési, jelentéskészítési és riasztási eszközként.

* [Az Azure-beli naplók figyelése és áttekintése API Management](./api-management-howto-use-azure-monitor.md)

* [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

* [Log Analytics munkaterület ismertetése](../azure-monitor/logs/log-analytics-tutorial.md)

* [Az Azure Application Insights integrációja](./api-management-howto-app-insights.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak engedélyezése, valamint az Azure API Management példányainak diagnosztikai beállításai, valamint a naplók elküldése egy log Analytics-munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján. A Log Analytics munkaterület-lekérdezések alapján létrehozhat riasztásokat.

Hozzon létre metrikus riasztásokat, hogy tudd, mikor történt váratlan esemény. Értesítéseket kaphat például arról, ha az Azure API Management-példánya egy adott időtartamon belül túllépte a várt maximális kapacitást, vagy ha egy meghatározott időn belül bizonyos számú jogosulatlan átjárói kérelem vagy hiba történt.

Opcionálisan integrálhatja API Management az Azure Application Insights-nal, és használhatja elsődleges vagy másodlagos figyelési, nyomkövetési, jelentéskészítési és riasztási eszközként.

Opcionálisan engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

* [Az Azure API Management diagnosztikai beállításainak engedélyezése](./api-management-howto-use-azure-monitor.md#activity-logs)

* [Riasztási szabály konfigurálása az Azure API Management](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule)

* [Azure API Management-példány kapacitási metrikáinak megtekintése](./api-management-capacity.md)

* [Az Azure Application Insights integrációja](./api-management-howto-app-insights.md)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; Az Azure API Management nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; Az Azure API Management nem dolgozza fel és nem hozza létre a felhasználók számára elérhető DNS-naplókat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure API Management vezérlési síkon (Azure Portal) rendszergazdai hozzáféréssel rendelkező fiókok leltárának fenntartása.

Azure Active Directory (AD) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. API Management ezekre a szerepkörökre támaszkodik, és Role-Based Access Control a API Management szolgáltatások és entitások részletes hozzáférés-kezelésének lehetővé tételéhez.

Emellett a API Management beépített rendszergazdák csoportot tartalmaz a API Management felhasználói rendszerében. A API Management csoportok az API-k láthatóságát vezérlik a fejlesztői portálon, és a rendszergazdák csoport tagjai láthatják az összes API-t.

A rendszergazdai fiókok kezelésével és karbantartásával kapcsolatban Azure Security Center ajánlásokat követve.

* [Szerepköralapú hozzáférés-vezérlés használata az Azure API Managementben](./api-management-role-based-access-control.md)

* [Azure API Management-példányban lévő felhasználók listájának beolvasása](/powershell/module/az.apimanagement/get-azapimanagementuser)

* [Címtárbeli szerepkörhöz rendelt felhasználók listájának beolvasása az Azure AD-ben a PowerShell-lel](/powershell/module/az.resources/get-azroleassignment)

* [Címtárbeli szerepkör-definíció beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/az.resources/get-azroledefinition)

* [A Azure Security Center identitás-és hozzáférési javaslatainak megismerése](../security-center/recommendations-reference.md#recs-identityandaccess)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az Azure API Management nem rendelkezik az alapértelmezett jelszó/kulcs fogalmával.

Az Azure API Management-előfizetések, amelyek az API-khoz való hozzáférés biztosítására szolgálnak, azonban a generált előfizetési kulcsokkal is rendelkeznek. Előfordulhat, hogy az ügyfelek bármikor újragenerálják ezeket az előfizetési kulcsokat.

* [Az Azure API Management-előfizetések ismertetése](./api-management-subscriptions.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

* [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](../security-center/security-center-identity-access.md)

* [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az Azure API Management konfigurálható úgy, hogy kihasználja az Azure Active Directory identitás-szolgáltatóként a felhasználók hitelesítéséhez a fejlesztői portálon, hogy kihasználhassa az Azure ad által kínált egyszeri bejelentkezés funkcióit. A konfigurálást követően az új fejlesztői portál felhasználói dönthetnek úgy, hogy az Azure AD-n keresztül először hitelesítik a bejelentkezési folyamatot, majd a hitelesítés után befejezik a regisztrációs folyamatot a portálon.

* [Fejlesztői fiókok engedélyezése az Azure Active Directory használatával az Azure API Managementben](./api-management-howto-aad.md)

Másik lehetőségként a bejelentkezési/regisztrációs folyamat tovább testreszabható a delegáláson keresztül. A delegálás lehetővé teszi, hogy meglévő webhelyét használja a fejlesztői bejelentkezéshez/regisztrációhoz és előfizetéshez a termékekhez, a fejlesztői portál beépített funkciójának használata helyett. Lehetővé teszi, hogy a webhelye a felhasználói adatok tulajdonosa legyen, és egyéni módon végezze el ezeknek a lépéseknek az érvényesítését.

* [Felhasználói regisztráció és termék-előfizetés delegálása](./api-management-howto-setup-delegation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory (AD) multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatainak követése.

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bevezetésére és konfigurálására konfigurált, multi-Factor Authentication (MFA) rendszerjogosultságú hozzáférési munkaállomások használata.

* [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

* [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést a Azure Portalhoz az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

* [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: amikor csak lehetséges, az Azure ad-t használhatja központi hitelesítési és engedélyezési rendszeren. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Konfigurálja az Azure API Management fejlesztői portált a fejlesztői fiókok Azure Active Directory használatával történő hitelesítéséhez.

Konfigurálja az Azure API Management-példányát úgy, hogy az OAuth 2,0 protokollt használja Azure Active Directory (AD) használatával az API-k biztosításához.

* [Fejlesztői fiókok engedélyezése Azure Active Directory használatával az Azure-ban API Management](./api-management-howto-aad.md)

* [API-k elleni védelem az OAuth 2,0 használatával Azure Active Directory és API Management](./api-management-howto-protect-backend-with-aad.md)

* [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory naplókat biztosít az elavult fiókok felderítéséhez. Az ügyfelek az Azure Identity hozzáférési felülvizsgálatait használhatják a csoporttagságok hatékony kezelésére, a vállalati alkalmazásokhoz való hozzáférésre és a szerepkör-hozzárendelésekre. A felhasználói hozzáférés rendszeres felülvizsgálata annak biztosítására, hogy csak a megfelelő felhasználók férhessenek hozzá a megfelelő hozzáféréshez.

Az ügyfelek igény szerint kezelhetik API Management felhasználói fiókjainak leltározását és a hozzáférés egyeztetését. API Management a fejlesztők a API Managementekkel elérhető API-k felhasználói. Alapértelmezés szerint az újonnan létrehozott fejlesztői fiókok aktívak, és a fejlesztői csoporthoz vannak társítva. Az aktív állapotban lévő fejlesztői fiókok az összes olyan API elérésére használhatók, amelyhez előfizetéssel rendelkeznek.

A rendszergazdák létrehozhatnak egyéni csoportokat, vagy külső csoportokat használhatnak a társított Azure Active Directory bérlők esetében. A fejlesztők mellett az egyéni és külső csoportoknak is lehet adni láthatóságot és hozzáférést az API-termékekhez.

* [Felhasználói fiókok kezelése az Azure API Managementben](./api-management-howto-create-or-invite-developers.md)

* [API Management-felhasználók listájának beolvasása](/powershell/module/az.apimanagement/get-azapimanagementuser)

* [Csoportok létrehozása és használata fejlesztői fiókok kezeléséhez az Azure API Managementben](./api-management-howto-create-groups.md)

* [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: az Azure API Management-példány konfigurálása a fejlesztői fiókok hitelesítésére az Azure-beli API Management Azure Active Directory identitás-szolgáltató használatával.

Konfigurálja az Azure API Management-példányát úgy, hogy az OAuth 2,0 protokollt használja Azure Active Directory (AD) használatával az API-k biztosításához.

Konfigurálja a JWT-érvényesítési házirendet a bejövő API-kérelmekre, hogy segítsen kényszeríteni egy érvényes jogkivonat létezését és érvényességét.

Diagnosztikai beállításokat hozhat létre az Azure AD felhasználói fiókjaihoz, és elküldheti a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. A kívánt riasztások konfigurálása Log Analyticson belül. Emellett a Log Analytics munkaterületet az Azure Sentinelbe vagy egy harmadik féltől származó SIEM-re is felveheti.

A speciális figyelést a API Management használatával konfigurálhatja a `log-to-eventhub` szabályzattal, rögzítheti a biztonsági elemzéshez szükséges további információkat, és elküldheti azokat az Azure Sentinel vagy a harmadik féltől származó Siem-nek.

* [Fejlesztői fiókok engedélyezése Azure Active Directory használatával az Azure-ban API Management](./api-management-howto-aad.md)

* [API-k elleni védelem az OAuth 2,0 használatával Azure Active Directory és API Management](./api-management-howto-protect-backend-with-aad.md)

* [API Management hozzáférés-korlátozási szabályzatok](./api-management-access-restriction-policies.md)

* [Az Azure AD-naplók integrálása a Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

* [API-k fejlett monitorozása](./api-management-log-to-eventhub-sample.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a fiók bejelentkezési viselkedése a vezérlési síkon (a Azure Portal) a Azure Active Directory (ad) Identity Protection és a kockázati észlelési funkciók használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

* [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

* [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: jelenleg nem érhető el; Az Azure API Management jelenleg nem támogatja a Ügyfélszéf.

* [Ügyfélszéf által támogatott szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az Azure API Management példányokat a Virtual Network (VNet)/subnet és a megfelelő címkével kell elválasztani.

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Az Azure API Management használata virtuális hálózatokkal](./api-management-using-with-vnet.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: jelenleg nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók jelenleg nem érhetők el az Azure API Management számára.

A Microsoft felügyeli az Azure API Management mögöttes infrastruktúráját, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a felügyeleti sík hívások Azure Resource Manager TLS-n keresztül történik. Érvényes JSON webes jogkivonat (JWT) szükséges. Az adatsík hívások a TLS-vel és az egyik támogatott hitelesítési mechanizmussal (például ügyféltanúsítvány vagy JWT) védhetők.

* [Az adatvédelem megismerése az Azure API Management](#data-protection)

* [TLS-beállítások kezelése az Azure-ban API Management](./api-management-howto-manage-protocols-ciphers.md)

* [Az API-k biztosítása az Azure API Managementban Azure Active Directory](./api-management-howto-protect-backend-with-aad.md)

* [Az API-k biztosítása az Azure API Managementban Azure Active Directory B2C](./howto-protect-backend-frontend-azure-ad-b2c.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: jelenleg nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók jelenleg nem érhetők el az Azure API Management számára. Címkézheti az Azure API Management-szolgáltatásokat, amelyek bizalmas adatokat dolgoznak fel, és amelyek a megfelelőség szempontjából szükségesek a harmadik féltől származó megoldások megvalósításához.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: szerepköralapú hozzáférés-vezérlés használata az Azure API Managementhoz való hozzáférés szabályozásához. Az Azure API Management az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével lehetővé teszi a részletes hozzáférés-kezelést a API Management-szolgáltatásokhoz és-entitásokhoz (például API-k és házirendek).

* [Szerepköralapú hozzáférés-vezérlés használata az Azure API Managementben](./api-management-role-based-access-control.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft felügyeli az Azure API Management mögöttes infrastruktúráját, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a bizalmas adatokat, például a tanúsítványokat, a kulcsokat és az elnevezett titkos kulcsot a szolgáltatás által felügyelt, a szolgáltatási példány kulcsaival titkosítja. Az összes titkosítási kulcs egy szolgáltatási példányon, a szolgáltatás pedig felügyelt.

* [Az Azure-API Management az adatvédelem/titkosítás megismerése](#data-protection)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a termelési Azure functions alkalmazásokra, valamint más kritikus vagy kapcsolódó erőforrásokra is vonatkoznak.

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

* [A Azure Monitor és az Azure-beli tevékenységek naplójának használata az Azure-ban API Management](./api-management-howto-use-azure-monitor.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: jelenleg nem érhető el; a sebezhetőségi felmérés Azure Security Center jelenleg nem érhető el az Azure API Management számára.

A Microsoft által beolvasott és javított mögöttes platform. Tekintse át a rendelkezésre álló biztonsági ellenőrzéseket a szolgáltatás konfigurálásával kapcsolatos biztonsági rések csökkentése érdekében.

* [Az Azure API Management számára elérhető biztonsági vezérlők ismertetése]()

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: jelenleg nem érhető el; a sebezhetőségi felmérés Azure Security Center jelenleg nem érhető el az Azure API Management számára.

A Microsoft által beolvasott és javított mögöttes platform. A szolgáltatás-konfigurációval kapcsolatos biztonsági rések csökkentése érdekében az ügyfelek áttekinthetik a számukra elérhető biztonsági vezérlőket.

* [Az Azure API Management számára elérhető biztonsági vezérlők ismertetése]()

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

* [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

* [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

* [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

* [Címkék létrehozása és felhasználása](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: az Azure-erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézést, a felügyeleti csoportokat és a különálló előfizetéseket, ahol szükséges. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésében létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: a felhasználók az Azure Resources Managerrel való interakcióra való képességének korlátozása parancsfájlok használatával

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

* [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

* [Szerepköralapú hozzáférés-vezérlés az Azure-ban API Management](./api-management-role-based-access-control.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: az Azure API Management szolgáltatás szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. ApiManagement" névtérben egyéni szabályzatok létrehozásához az Azure API Management-szolgáltatások konfigurációjának naplózásához vagy érvényesítéséhez.

* [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure API Management-szolgáltatások szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. ApiManagement" névtérben egyéni szabályzatok létrehozásához az Azure API Management-példányok konfigurációjának naplózásához vagy érvényesítéséhez. Az Azure-szabályzat [deny] és a [telepítés, ha nem létezik] használatával kényszerítheti ki az Azure-erőforrások biztonságos beállításait.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: Ha egyéni Azure-szabályzat-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti az Azure API Management szolgáltatás konfigurációját.

* [Fájlok tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Az Azure Repos dokumentációja](/azure/devops/repos/index)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: az Azure API Management-szolgáltatások szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. ApiManagement" névtérben egyéni szabályzatok létrehozásához az Azure API Management-példányok konfigurációjának naplózásához vagy érvényesítéséhez. Az Azure-szabályzat [deny] és a [telepítés, ha nem létezik] használatával kényszerítheti ki az Azure-erőforrások biztonságos beállításait.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: az Azure API Management DevOps Resource Kit használatával végezheti el az azure-API Management konfigurálását.

Emellett az Azure API Management-szolgáltatások szabványos biztonsági konfigurációit is meghatározhatja és implementálhatja a Azure Policy. Használjon Azure Policy aliasokat a "Microsoft. ApiManagement" névtérben egyéni szabályzatok létrehozásához az Azure API Management-példányok konfigurációjának naplózásához vagy érvényesítéséhez. Az Azure-szabályzat [deny] és a [telepítés, ha nem létezik] használatával kényszerítheti ki az Azure-erőforrások biztonságos beállításait.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Key Vault használata a tanúsítványok kezeléséhez és az autoforgatáshoz való beállításához. Ha az egyéni tartomány SSL-tanúsítványának kezeléséhez Azure Key Vault használ, győződjön meg róla, hogy a tanúsítvány a Key Vault tanúsítványként, nem pedig titkosként van beillesztve.

* [Egyéni tartománynevek beállítása útmutatással Key Vault kulcs elforgatásához](./configure-custom-domain.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a Azure Active Directory (ad) által generált Managed Service Identity használata lehetővé teszi, hogy a API Management-példány könnyen és biztonságosan hozzáférhessen más Azure ad-védelemmel ellátott erőforrásokhoz, például a Azure Key Vaulthoz.

* [Felügyelt identitás létrehozása API Management-példányhoz](./api-management-howto-use-managed-service-identity.md)

* [Házirend a felügyelt identitással történő hitelesítéshez](./api-management-authentication-policies.md#ManagedIdentity)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

* [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure API Management), azonban nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: nem alkalmazható; Ez a javaslat az adatok tárolására szolgáló, nem számítási erőforrásokra szolgál.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure API Management), azonban nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat az adatok tárolására szolgáló, nem számítási erőforrásokra szolgál.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure API Management), azonban nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: az API-k Azure API Management használatával történő közzétételével és kezelésével kihasználhatja a hibatűrést és az infrastruktúra azon funkcióit, amelyeket egyébként megtervezni, megvalósítani és felügyelni. API Management támogatja a többrégiós telepítést, amely az adatközpontot a regionális hibákba való áteresztő képesség nélkül teszi lehetővé.

A API Management szolgáltatás biztonsági mentési és visszaállítási funkciói biztosítják a vész-helyreállítási stratégia megvalósításához szükséges építőelemeket. A biztonsági mentési és visszaállítási műveletek manuálisan vagy automatizáltan is elvégezhetők.

* [API Management adatsík üzembe helyezése több régióban](./api-management-howto-deploy-multi-region.md)

* [Vészhelyreállítás a biztonsági mentés és visszaállítás használatával az Azure API Managementben](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [A API Management biztonsági mentési művelet meghívása](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

* [A API Management visszaállítási műveletének meghívása](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: az Azure API Management által biztosított biztonsági mentési és visszaállítási műveletek teljes körű biztonsági mentést és visszaállítást végeznek.

A felügyelt identitások használatával Azure Key Vault tanúsítványok szerezhetők be API Management egyéni tartománynevek számára. Biztonsági másolat készítése a Azure Key Vaulton belül tárolt tanúsítványokról.

* [Vészhelyreállítás a biztonsági mentés és visszaállítás használatával az Azure API Managementben](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Azure Key Vault tanúsítványok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: biztonsági mentések ellenőrzése a szolgáltatás és a tanúsítványok biztonsági másolatokból történő visszaállításával.

* [A API Management visszaállítási műveletének meghívása](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

* [Azure Key Vault tanúsítványok visszaállítása](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure API Management a biztonsági mentéseket az ügyfél által birtokolt Azure Storage-fiókba írja. A biztonsági mentés védelme érdekében kövesse az Azure Storage biztonsági javaslatait.

* [Vészhelyreállítás a biztonsági mentés és visszaállítás használatával az Azure API Managementben](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [A blob Storage biztonsági javaslata](../storage/blobs/security-recommendations.md)

A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához engedélyezze a Key Vault Soft-Delete.

* [A Soft-Delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

* [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [A NIST számítógépes biztonsági incidensek kezelésének útmutatója a saját incidensekre vonatkozó válaszadási terv létrehozásához nyújtott támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

* [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

* [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

* [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

* [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

* [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

* [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciójának használatával automatikusan elindíthatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

* [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat kijavítása az 60 napon belül

**Útmutató**: * [kérjük, kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [A Microsoft által felügyelt felhő-infrastruktúrával,-szolgáltatásokkal és-alkalmazásokkal kapcsolatos, a Microsoft által kezelt felhőalapú infrastruktúra, szolgáltatások](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
