---
title: Azure Functions Azure biztonsági alapterve
description: Azure Functions Azure biztonsági alapterve
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b57de23bf59f1b9c84674fe95495f980c4594e2a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587605"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Azure Functions Azure biztonsági alapterve

Az Azure Functions Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: az [Azure biztonsági alaptervek áttekintése](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: Azure functions-alkalmazások integrálása Azure-beli virtuális hálózattal. A prémium szintű csomagban futó functions-alkalmazások ugyanazokat az üzemeltetési funkciókat rendelkeznek, mint a Azure App Service webalkalmazásai, beleértve az "VNet-integráció" funkciót is.  Az Azure Virtual Networks lehetővé teszi, hogy számos Azure-erőforrást, például Azure Functions helyezzen el egy nem internetes útválasztású hálózatban.

- [Függvények integrálása Azure-Virtual Network](./functions-create-vnet.md)

- [A Azure Functions és Azure App Service vnet-integrációjának megismerése](../app-service/web-sites-integrate-with-vnet.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózati védelmi javaslatok követése a Azure functions alkalmazásokhoz kapcsolódó hálózati erőforrások és hálózati konfigurációk biztonságossá tételéhez.

Ha hálózati biztonsági csoportokat (NSG) használ a Azure Functions megvalósításával, engedélyezze a NSG flow-naplókat, és küldje el a naplókat egy Azure Storage-fiókba a forgalmi auditok számára. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az Azure functions-végpontok éles környezetben való teljes biztonságossá tételéhez érdemes megfontolni a következő Function szintű biztonsági beállítások egyikének megvalósítását:
- App Service hitelesítés/engedélyezés bekapcsolása a Function alkalmazáshoz
- Az Azure API Management (APIM) használatával hitelesítheti a kérelmeket, vagy
- A Function alkalmazás üzembe helyezése egy Azure App Service Environment.

Továbbá győződjön meg arról, hogy a távoli hibakeresés le van tiltva az éles Azure Functions. Emellett a több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára az Azure-beli Function app elérését. Csak a szükséges tartományokat engedélyezze a Function alkalmazással való kommunikációhoz.

A bejövő forgalom további ellenőrzéséhez vegye fontolóra az Azure webalkalmazási tűzfal (WAF) üzembe helyezését a hálózati konfiguráció részeként. Diagnosztikai beállítás engedélyezése a WAF és a naplók betöltéséhez egy Storage-fiókba, az Event hub-ba vagy a Log Analytics-munkaterületre. 

- [Azure Functions végpontok biztonságossá tétele az éles környezetben](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: DDoS Protection standard engedélyezése a functions-alkalmazásokhoz társított virtuális hálózatokon a DDOS-támadások elleni védelem érdekében. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt nyilvános IP-címekkel.
Emellett konfigurálhat egy előtér-átjárót, például az Azure webalkalmazási tűzfalat az összes bejövő kérelem hitelesítéséhez és a kártékony forgalom kiszűréséhez. Az Azure webalkalmazási tűzfal a beérkező webes forgalomnak az SQL-injektálások, a helyek közötti parancsfájlok, a kártevő-feltöltések és a DDoS-támadások letiltására való megadásával segíti a Function app védelmét. A WAF bevezetéséhez App Service Environment vagy privát végpontok (előzetes verzió) használata szükséges. Az éles munkaterhelések használata előtt győződjön meg arról, hogy a magánhálózati végpontok már nem (előzetes verzió).

- [Az Azure Functions hálózatkezelési lehetőségei](./functions-networking-options.md)

- [Prémium csomag Azure Functions](./functions-premium-plan.md)

- [Az App Service Environment bemutatása](../app-service/environment/intro.md)

- [App Service-környezet hálózati megfontolásai](../app-service/environment/network-info.md)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

- [Azure Security Center adaptív hálózat megerősítésének ismertetése](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center az időponthoz tartozó hálózati Access Control ismertetése](../security-center/security-center-just-in-time.md)

- [Privát végpontok használata Azure Functionshoz](../app-service/networking/private-endpoint.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: Ha hálózati biztonsági csoportokat (NSG) használ a Azure functions implementációval, engedélyezze a hálózati biztonsági csoport folyamatábráit, és küldje el a naplókat a forgalmi naplózáshoz a Storage-fiókba. A flow-naplókat Log Analytics munkaterületre is elküldheti, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: egy előtér-átjáró, például az Azure webalkalmazási tűzfal konfigurálása az összes bejövő kérelem hitelesítéséhez, valamint a kártékony forgalom kiszűrése. Az Azure webalkalmazási tűzfal a beérkező webes forgalom védelme érdekében az SQL-injektálások, a helyek közötti parancsfájlok, a kártevő-feltöltések és a DDoS-támadások letiltásával segíti a funkciós alkalmazások védelmét. A WAF bevezetéséhez App Service Environment vagy privát végpontok (előzetes verzió) használata szükséges. Az éles munkaterhelések használata előtt győződjön meg arról, hogy a magánhálózati végpontok már nem (előzetes verzió).

Azt is megteheti, hogy több Piactéri lehetőség áll rendelkezésre, például az Azure-hoz készült Barracuda-WAF, amelyek az Azure piactéren érhetők el, beleértve az AZONOSÍTÓk/IP-

- [Az Azure Functions hálózatkezelési lehetőségei](./functions-networking-options.md)

- [Prémium csomag Azure Functions](./functions-premium-plan.md)

- [Az App Service Environment bemutatása](../app-service/environment/intro.md)

- [App Service-környezet hálózati megfontolásai](../app-service/environment/network-info.md)

- [Az Azure webalkalmazási tűzfal ismertetése](../web-application-firewall/index.yml)

- [Privát végpontok használata Azure Functionshoz](../app-service/networking/private-endpoint.md)

- [A Barracuda WAF Cloud Service ismertetése](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: konfiguráljon egy előtér-átjárót a hálózatához, például az Azure webalkalmazási tűzfalat a végpontok közötti TLS-titkosítással. A WAF bevezetéséhez App Service Environment vagy privát végpontok (előzetes verzió) használata szükséges. Az éles munkaterhelések használata előtt győződjön meg arról, hogy a magánhálózati végpontok már nem (előzetes verzió).

- [Az Azure Functions hálózatkezelési lehetőségei](./functions-networking-options.md)

- [Prémium csomag Azure Functions](./functions-premium-plan.md)

- [Az App Service Environment bemutatása](../app-service/environment/intro.md)

- [App Service-környezet hálózati megfontolásai](../app-service/environment/network-info.md)

- [Az Azure webalkalmazási tűzfal ismertetése](../web-application-firewall/index.yml)

- [Végpontok közötti TLS konfigurálása Application Gateway használatával a portálon](../application-gateway/end-to-end-ssl-portal.md)

- [Privát végpontok használata Azure Functionshoz](../app-service/networking/private-endpoint.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használjon Virtual Network szolgáltatás-címkéket. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például AzureAppService), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [További információ a szolgáltatási címkék használatáról](../virtual-network/service-tags-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a Azure Functionshöz kapcsolódó hálózati beállításokhoz. Használjon Azure Policy aliasokat a "Microsoft. Web" és a "Microsoft. Network" névterekben, hogy egyéni házirendeket hozzon létre a Azure Functions hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a Azure Functions beépített szabályzat-definícióját is, például:
- A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a függvény alkalmazásaihoz
- A Function alkalmazás csak HTTPS protokollon keresztül érhető el
- A legújabb TLS-verziót kell használni a Function alkalmazásban

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagy léptékű Azure-környezeteket a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek egyetlen terv szerinti definícióban való kicsomagolásával. A tervet egyszerűen alkalmazhatja új előfizetésekre, környezetekre, valamint a verziószámozáson keresztül történő finomhangolásra és felügyeletre.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutatás**: Ha hálózati biztonsági csoportokat (NSG) használ a Azure functions megvalósításához, a NSG és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz használjon címkéket. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti a hálózati erőforrás-konfigurációkat, és felderítheti a Azure functions üzemelő példányokhoz kapcsolódó hálózati beállítások és erőforrások változásait. Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor aktiválódnak, ha a kritikus hálózati beállítások vagy erőforrások változása zajlik. 

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrásokhoz használt időforrást, például az Azure functions a naplókban található időbélyegek esetében.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

A Azure Functions az Azure Application Insights beépített integrációját is biztosítja a függvények figyeléséhez. Application Insights gyűjti a napló-, a teljesítmény-és a hiba adatait. A szolgáltatás automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és a függvények használatának megismerésében.

Ha beépített egyéni biztonsági/naplózási naplózást végez a Function alkalmazásban, engedélyezze a "FunctionAppLogs" diagnosztikai beállítást, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. 

Opcionálisan engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Azure Functions beállítása az Azure Application Insights](./functions-monitoring.md)

- [Diagnosztikai beállítások (felhasználó által generált naplók) engedélyezése Azure Functions számára](./functions-monitor-log-analytics.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

Ha beépített egyéni biztonsági/naplózási naplózást végez a Function alkalmazásban, engedélyezze a "FunctionAppLogs" diagnosztikai beállítást, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. 

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Diagnosztikai beállítások (felhasználó által generált naplók) engedélyezése Azure Functions számára](./functions-monitor-log-analytics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitorban a szervezet megfelelőségi előírásai szerint állítsa be a függvények alkalmazásaihoz társított log Analytics-munkaterületek naplózásának megőrzési időtartamát.

- [Napló-megőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak, valamint a Function alkalmazás diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján.

Engedélyezze Application Insights a Function apps számára a naplók, a teljesítmény és a hiba adatainak gyűjtéséhez. A Azure Portalon belül Application Insights gyűjtött telemetria-adatokat tekintheti meg.

Ha beépített egyéni biztonsági/naplózási naplózást végez a Function alkalmazásban, engedélyezze a "FunctionAppLogs" diagnosztikai beállítást, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. 

Opcionálisan engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [A Azure Functions diagnosztikai beállításainak engedélyezése](./functions-monitor-log-analytics.md)

- [A Azure Functions beállítása az Azure Application Insights és a telemetria-adatkezelés megtekintése](./functions-monitoring.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak, valamint a Function alkalmazás diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján. A Log Analytics munkaterület-lekérdezések alapján létrehozhat riasztásokat.

Engedélyezze Application Insights a Function apps számára a naplók, a teljesítmény és a hiba adatainak gyűjtéséhez. Megtekintheti Application Insights által gyűjtött telemetria-adatokat, és riasztásokat hozhat létre a Azure Portalon belül.

Opcionálisan engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [A Azure Functions diagnosztikai beállításainak engedélyezése](./functions-monitor-log-analytics.md)

- [A Azure Functions Application Insights engedélyezése](./configure-monitoring.md#enable-application-insights-integration)

- [Riasztások létrehozása az Azure-ban](../azure-monitor/alerts/tutorial-response.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; a Function apps nem dolgoz fel és nem hoz létre kártevő-ellenes kapcsolódó naplókat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; a Function apps nem dolgozza fel vagy nem hozza létre a felhasználók számára elérhető DNS-naplókat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Active Directory (ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez. 

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Azure Functionshoz való hozzáférés szabályozása Azure Active Directory (ad) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az adatsíkok hozzáférése több módon is szabályozható, beleértve az engedélyezési kulcsokat, a hálózati korlátozásokat és az Azure AD-identitások érvényességét. Az engedélyezési kulcsokat a Azure Functions HTTP-végpontokhoz csatlakozó ügyfelek használják, és bármikor újra létrehozhatók. Ezek a kulcsok alapértelmezés szerint jönnek létre az új HTTP-végpontokhoz.

Több üzembe helyezési módszer is elérhető az alkalmazások működéséhez, amelyek némelyike generált hitelesítő adatok készletét is kihasználhatja. Tekintse át az alkalmazáshoz használt üzembe helyezési módszereket.

- [HTTP-végpont biztonságossá tétele](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Engedélyezési kulcsok beszerzése és újragenerálása](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Üzembe helyezési technológiák Azure Functions](./functions-deployment-technologies.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében a Azure Security Center vagy beépített Azure-szabályzatokból is használhat javaslatokat, például: a tulajdonosi engedélyekkel rendelkező elavult fiókok esetében több tulajdonost is el kell távolítani az előfizetéshez tartozó külső fiókokból.

- [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: ha lehetséges, használja a Azure Active Directory SSO-t ahelyett, hogy egyéni önálló hitelesítő adatokat konfiguráljon a Function alkalmazáshoz való hozzáféréshez. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata. Az App Service hitelesítés/engedélyezés funkcióval egyszeri bejelentkezést valósíthat meg a Function apps-alkalmazásokhoz.

- [A hitelesítés és az engedélyezés ismertetése Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory (AD) multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatainak követése.

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bevezetésére és konfigurálására konfigurált, multi-Factor Authentication (MFA) rendszerjogosultságú hozzáférési munkaállomások használata.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést a Azure Portalhoz az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerként a Function apps-alkalmazásokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [A Function app konfigurálása az Azure AD-bejelentkezés használatára](../app-service/configure-authentication-provider-aad.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. 

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerként a Function apps-alkalmazásokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Hozzáférése van az Azure AD bejelentkezési tevékenységeihez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik az Azure Sentinel vagy egy harmadik féltől származó SIEM integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics belül is konfigurálhatja a kívánt naplózási riasztásokat.

- [A Function app konfigurálása az Azure AD-bejelentkezés használatára](../app-service/configure-authentication-provider-aad.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerként a Function apps-alkalmazásokhoz. A vezérlési síkon (a Azure Portal) való bejelentkezéshez használja a Azure Active Directory (AD) Identity Protection és a kockázati észlelési funkciókat a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszok konfigurálásához. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: jelenleg nem érhető el; A Ügyfélszéf jelenleg nem támogatott Azure Functions esetén.

- [Ügyfélszéf által támogatott szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. a Function apps-alkalmazásokat a virtuális hálózat (VNet)/subnet kell elválasztani, és megfelelően címkézve.

A hálózati elkülönítés végrehajtásához privát végpontokat is használhat. Az Azure Private-végpontok olyan hálózati adapterek, amelyek az Azure privát kapcsolaton keresztül csatlakoznak egy szolgáltatáshoz (például: Function app HTTPs-végpont). A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így lényegében bekapcsolja a szolgáltatást a virtuális hálózatba. A privát végpontok (előzetes verzió) a prémium csomagon futó Function apps szolgáltatásokhoz tartoznak. Az éles munkaterhelések használata előtt győződjön meg arról, hogy a magánhálózati végpontok már nem (előzetes verzió).

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Az Azure Functions hálózatkezelési lehetőségei](./functions-networking-options.md)

- [Prémium csomag Azure Functions](./functions-premium-plan.md)

- [Privát végpont ismertetése](../private-link/private-endpoint-overview.md)

- [Privát végpontok használata Azure Functionshoz](../app-service/networking/private-endpoint.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: automatikus eszköz üzembe helyezése olyan hálózati peremhálózaton, amely figyeli a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztást küld az információs biztonsági szakemberek számára. 

A Microsoft kezeli a Azure Functions alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: nem alkalmazható

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a function apps Azure Portal a "platform szolgáltatások: HÁLÓZATKEZELÉS: SSL" területen engedélyezze a "csak https" beállítást, és állítsa be a minimális TLS-verziót 1,2-re.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: jelenleg nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók jelenleg nem érhetők el Azure Functions. Az olyan alkalmazások címkézése, amelyek a bizalmas adatokat feldolgozzák, és ha a megfelelőség szempontjából szükséges, harmadik féltől származó megoldást is megvalósítanak.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozhatja a Function app Control Plane (a Azure Portal) elérését. 

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

A Microsoft kezeli a Azure Functions alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Function app létrehozásakor létre kell hoznia vagy hivatkoznia kell egy általános célú Azure Storage-fiókra, amely támogatja a blob, a üzenetsor és a Table Storage használatát. Ennek az az oka, hogy a függvények az Azure Storage-on alapulnak olyan műveletekre, mint például az eseményindítók és a naplózási függvények végrehajtásának kezelése. Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az ügyfél által felügyelt kulcsokat a blobok és a fájlok titkosításához. Ezeknek a kulcsoknak jelen kell lennie Azure Key Vaultban, hogy a Function alkalmazás hozzáférhessen a Storage-fiókhoz.

- [A Azure Functions tárolási szempontjainak ismertetése](./storage-considerations.md)

- [Az Azure Storage-titkosítás megismerése a REST-adatok esetében](../storage/common/storage-service-encryption.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a termelési funkció alkalmazásaiban, valamint egyéb kritikus vagy kapcsolódó erőforrásokban is érvényben vannak.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: hozzon létre egy DevSecOps, amely biztosítja, hogy a függvény alkalmazásai biztonságosak legyenek, és a lehető legbiztonságosabbak maradjanak életciklusuk időtartama alatt. A DevSecOps beépíti a szervezete biztonsági csapatát és képességeiket a DevOps-gyakorlatba, így a csapaton belül mindenki felelős a biztonságért.

Emellett a Azure Security Center javaslatainak követésével biztonságosabbá teheti a Function-alkalmazásokat.

- [Folyamatos biztonsági ellenőrzés hozzáadása a CI/CD-folyamathoz](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának üzembe helyezése

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Microsoft a biztonsági rések kezelését a Azure functions támogató mögöttes rendszereken hajtja végre, azonban a javaslatok súlyosságát Azure Security Center, valamint a biztonságos pontszámot is használhatja a környezeten belüli kockázat méréséhez. A biztonságos pontszám azon alapul, hogy hány Security Center javasolt javaslat. Az első megoldáshoz szükséges javaslatok rangsorolása érdekében vegye figyelembe az egyes problémák súlyosságát.

- [Biztonsági javaslatok hivatkozási útmutatója](../security-center/recommendations-reference.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

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

Emellett az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára, a következő beépített szabályzat-definíciók használatával: nem engedélyezett erőforrástípusok nem engedélyezettek.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: jóváhagyott Azure-erőforrások és jóváhagyott szoftverek definiálása számítási erőforrásokhoz.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára. 

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával: nem engedélyezett erőforrástípusok engedélyezett erőforrástípus

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: a felhasználók az Azure Resources Managerrel való interakcióra való képességének korlátozása parancsfájlok használatával

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: a bizalmas vagy magas kockázatú függvények alkalmazásai esetében külön előfizetéseket és/vagy felügyeleti csoportokat kell megvalósítani az elkülönítés biztosításához.

Magas kockázatú Function-alkalmazásokat telepíthet a saját Virtual Networkba (VNet). A virtuális hálózatok keresztül érhető el a Function apps peremhálózati biztonsági funkciója. A prémium csomagon vagy App Service Environmenton (virtuális hálózatok) futó függvények integrálható a szolgáltatásba. Válassza ki a legjobb architektúrát a használati esethez.

- [Az Azure Functions hálózatkezelési lehetőségei](./functions-networking-options.md)

- [Prémium csomag Azure Functions](./functions-premium-plan.md)

- [App Service-környezet hálózati megfontolásai](../app-service/environment/network-info.md)

- [Külső beszerzési útmutató létrehozása](../app-service/environment/create-external-ase.md)

Belső beszerzési útmutató létrehozása:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Function alkalmazás szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Web" névtérben, hogy egyéni szabályzatokat hozzon létre a Function apps konfigurációjának naplózásához vagy érvényesítéséhez. Olyan beépített szabályzat-definíciókat is használhat, mint például a következők:
- A felügyelt identitást a Function alkalmazásban kell használni
- A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban
- A Function alkalmazás csak HTTPS protokollon keresztül érhető el

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés, ha nem létezik] az Azure-erőforrások biztonságos beállításainak betartatására használható.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Habár lehetséges a helyszíni függvények üzembe helyezése is, ez az útmutató a számítási erőforrások IaaS szolgál. A helyszíni függvények telepítésekor Ön felelős a környezete biztonságos konfigurációjától.

- [A helyszíni függvények ismertetése](./functions-runtime-install.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ARM-sablonok és egyéni Azure-szabályzat-definíciók biztonságos tárolása és kezelése a verziókövetés számára.

- [Mi az infrastruktúra mint kód](/azure/devops/learn/what-is-infrastructure-as-code)

- [Szabályzat tervezése kód-munkafolyamatként](../governance/policy/concepts/policy-as-code.md)

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: beépített Azure Policy definíciók és Azure Policy aliasok használata a "Microsoft. Web" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: beépített Azure Policy definíciók és Azure Policy aliasok használata a "Microsoft. Web" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Az Azure Policy [audit], [megtagadás] és [telepítés ha nem létezik] használatával automatikusan kényszerítheti ki az Azure-erőforrások konfigurációit.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: felügyelt identitások használata a Azure Key Vaultkel együtt a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében. A felügyelt identitások lehetővé teszik a Function app számára, hogy hitelesítse az Azure AD-hitelesítést támogató szolgáltatásait, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Key Vault létrehozása](../key-vault/secrets/quick-create-portal.md)

- [Felügyelt identitások használata App Service és Azure Functions](../app-service/overview-managed-identity.md)

* [Hitelesítés Key Vault](../key-vault/general/authentication.md)

* [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

- [Key Vault referenciák használata App Service és Azure Functions](../app-service/app-service-key-vault-references.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja a Function app számára az automatikusan felügyelt identitást az Azure ad-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitások használata App Service és Azure Functions](../app-service/overview-managed-identity.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Functions), azonban az nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: nem alkalmazható; Ez a javaslat az adatok tárolására szolgáló, nem számítási erőforrásokra szolgál.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat az adatok tárolására szolgáló, nem számítási erőforrásokra szolgál.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Functions), azonban az nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: az alkalmazás rendszeres biztonsági mentésének ütemezéséhez használja a Backup és a Restore funkciót. A prémium szintű csomagban futó functions-alkalmazások ugyanazokat az üzemeltetési funkciókat rendelkeznek, mint a Azure App Service webalkalmazásai, beleértve a "biztonsági mentés és visszaállítás" funkciót is.

Olyan verziókövetés-megoldást is használhat, mint például az Azure Repos és az Azure DevOps a kód biztonságos tárolásához és kezeléséhez. Az Azure DevOps Services számos Azure Storage-funkciót használ, hogy gondoskodni tudjon az adatok rendelkezésre állásáról hardverhiba, szolgáltatáskimaradás vagy régiós katasztrófa esetén. Az Azure DevOps csapata olyan eljárásokat is követ, amelyek a véletlen vagy rosszindulatú törlésektől védi az adatokat.

- [Adatok biztonsági mentése az Azure-ban](../app-service/manage-backup.md)

- [Az adatelérhetőség ismertetése az Azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: az alkalmazás rendszeres biztonsági mentésének ütemezéséhez használja a Backup és a Restore funkciót. A prémium szintű csomagban futó functions-alkalmazások ugyanazokat az üzemeltetési funkciókat rendelkeznek, mint a Azure App Service webalkalmazásai, beleértve a "biztonsági mentés és visszaállítás" funkciót is. Ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül.

Olyan verziókövetés-megoldást is használhat, mint például az Azure Repos és az Azure DevOps a kód biztonságos tárolásához és kezeléséhez. Az Azure DevOps Services számos Azure Storage-funkciót használ, hogy gondoskodni tudjon az adatok rendelkezésre állásáról hardverhiba, szolgáltatáskimaradás vagy régiós katasztrófa esetén. Az Azure DevOps csapata olyan eljárásokat is követ, amelyek a véletlen vagy rosszindulatú törlésektől védi az adatokat.

- [Adatok biztonsági mentése az Azure-ban](../app-service/manage-backup.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Az adatelérhetőség ismertetése az Azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Ellenőrizze, hogy a biztonsági mentés és visszaállítás szolgáltatás rendszeres időközönként hogyan végezheti el a visszaállítást. Ha egy másik offline helyet használ a kód biztonsági mentéséhez, rendszeres időközönként gondoskodjon a teljes helyreállítások végrehajtásáról. A biztonsági másolatok által felügyelt kulcsok visszaállításának tesztelése.

- [Azure-beli alkalmazás visszaállítása biztonsági másolatból](../app-service/web-sites-restore.md)

- [Azure-beli alkalmazás visszaállítása pillanatképből](../app-service/app-service-web-restore-snapshots.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a biztonsági mentés és visszaállítás funkció biztonsági mentései egy Azure Storage-fiókot használnak az előfizetésében. Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az ügyfél által felügyelt kulcsokat a tárolási adat titkosításához.

Ha ügyfél által felügyelt kulcsokat használ, győződjön meg arról, hogy a Key Vaultban Soft-Delete engedélyezve van a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemhez.

- [Azure Storage-titkosítás inaktív állapotban](../storage/common/storage-service-encryption.md)

- [A Soft-Delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a biztonsági riasztásokra és a Logic Appsokkal kapcsolatos ajánlásokra adott válaszokat.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
