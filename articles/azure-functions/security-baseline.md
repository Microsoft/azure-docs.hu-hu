---
title: Azure Functions Azure biztonsági alapterve
description: A Azure Functions biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4fd53067309f83b284da25040f9f6534936cead9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704637"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Azure Functions Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Azure Functionsre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure functions vonatkozó kapcsolódó útmutatás. A Azure Functions nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Azure Functions teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure functions biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: Azure functions-alkalmazások integrálása Azure-beli virtuális hálózattal. A prémium szintű csomagban futó functions-alkalmazások ugyanazokat az üzemeltetési funkciókat rendelkeznek, mint a Azure App Service webalkalmazásai, beleértve az "VNet-integráció" funkciót is.  Az Azure Virtual Networks lehetővé teszi, hogy számos Azure-erőforrást, például Azure Functions helyezzen el egy nem internetes útválasztású hálózatban.

- [Függvények integrálása Azure-Virtual Network](functions-create-vnet.md)

- [A Azure Functions és Azure App Service vnet-integrációjának megismerése](../app-service/web-sites-integrate-with-vnet.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózati védelmi javaslatok követése a Azure functions alkalmazásokhoz kapcsolódó hálózati erőforrások és hálózati konfigurációk biztonságossá tételéhez.

Ha hálózati biztonsági csoportokat használ a Azure Functions implementációval, engedélyezze a hálózati biztonsági csoportok adatfolyam-naplóit, és küldje el a naplókat egy Azure Storage-fiókba a forgalmi auditok számára. Hálózati biztonsági csoportokat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az Azure functions-végpontok éles környezetben való teljes biztonságossá tételéhez érdemes megfontolni a következő Function szintű biztonsági beállítások egyikének megvalósítását:

- App Service hitelesítés vagy engedélyezés bekapcsolása a Function alkalmazáshoz

- A kérelmek hitelesítése az Azure API Management (APIM) használatával

- A Function alkalmazás üzembe helyezése egy Azure App Service Environment.

Továbbá győződjön meg arról, hogy a távoli hibakeresés le van tiltva az éles Azure Functions. Emellett a több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára az Azure-beli Function app elérését. Csak a szükséges tartományokat engedélyezze a Function alkalmazással való kommunikációhoz.

A bejövő forgalom további ellenőrzéséhez vegye fontolóra az Azure webalkalmazási tűzfal (WAF) üzembe helyezését a hálózati konfiguráció részeként. Diagnosztikai beállítás engedélyezése a WAF és a naplók betöltéséhez egy Storage-fiókba, az Event hub-ba vagy a Log Analytics-munkaterületre. 

- [Azure Functions végpontok biztonságossá tétele az éles környezetben](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az DDoS Protection standard engedélyezése a Function appshez társított virtuális hálózatokon a DDOS-támadások elleni védelem érdekében. A veszélyforrások elleni intelligenciát Azure Security Center integráltan használhatja, hogy megtagadja a kommunikációt az ismert kártékony vagy nem használt nyilvános IP-címekkel.

Emellett konfigurálhat egy előtér-átjárót, például az Azure webalkalmazási tűzfalat az összes bejövő kérelem hitelesítéséhez és a kártékony forgalom kiszűréséhez. Az Azure webalkalmazási tűzfal a beérkező webes forgalomnak az SQL-injektálások, a helyek közötti parancsfájlok, a kártevő-feltöltések és a DDoS-támadások letiltására való megadásával segíti a Function app védelmét. A webalkalmazási tűzfal bevezetéséhez vagy egy App Service Environment vagy privát végpontok használatára van szükség. A prémium és App Service csomagokban üzemeltetett függvények saját végpontokat is használhatnak.

- [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutatás**: Ha hálózati biztonsági csoportot használ a Azure functions implementációval, engedélyezze a hálózati biztonsági csoport folyamatábráit, és küldje el a naplókat a forgalmi naplózáshoz a Storage-fiókba. A flow-naplókat Log Analytics munkaterületre is elküldheti, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: egy előtér-átjáró, például az Azure webalkalmazási tűzfal konfigurálása az összes bejövő kérelem hitelesítéséhez, valamint a kártékony forgalom kiszűrése. Az Azure webalkalmazási tűzfal a beérkező webes forgalom védelme érdekében az SQL-injektálások, a helyek közötti parancsfájlok, a kártevő-feltöltések és a DDoS-támadások letiltásával segíti a funkciós alkalmazások védelmét. A webalkalmazási tűzfal bevezetéséhez vagy egy App Service Environment vagy privát végpontok használatára van szükség. A prémium és App Service csomagokban üzemeltetett függvények saját végpontokat is használhatnak.

Azt is megteheti, hogy több Piactéri lehetőség áll rendelkezésre, például a Barracuda webalkalmazási tűzfal az Azure-hoz, amely az Azure Marketplace-en érhető el, és tartalmazza a behatolás-észlelési vagy

- [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)

- [Prémium csomag Azure Functions](functions-premium-plan.md)

- [Az App Service Environment bemutatása](../app-service/environment/intro.md)

- [App Service-környezet hálózati megfontolásai](../app-service/environment/network-info.md) 

- [Az Azure webalkalmazási tűzfal ismertetése](../web-application-firewall/overview.md)

- [Privát végpontok használata Azure Functionshoz](../app-service/networking/private-endpoint.md)

- [A Barracuda WAF Cloud Service ismertetése](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: konfiguráljon egy előtér-átjárót a hálózatához, például az Azure webalkalmazási tűzfalat a végpontok közötti TLS-titkosítással. A webalkalmazási tűzfal bevezetéséhez vagy egy App Service Environment vagy privát végpontok használatára van szükség. A prémium és App Service csomagokban üzemeltetett függvények saját végpontokat is használhatnak.

- [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)

- [Prémium csomag Azure Functions](functions-premium-plan.md)

- [Az App Service Environment bemutatása](../app-service/environment/intro.md)

- [App Service-környezet hálózati megfontolásai](../app-service/environment/network-info.md) 

- [Az Azure webalkalmazási tűzfal ismertetése](../web-application-firewall/overview.md)

- [Végpontok közötti TLS konfigurálása Application Gateway használatával a portálon](../application-gateway/end-to-end-ssl-portal.md)

- [Privát végpontok használata Azure Functionshoz](../app-service/networking/private-endpoint.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a hálózati biztonsági csoport vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használjon Virtual Network szolgáltatás-címkéket. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például AzureAppService), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [További információ a szolgáltatási címkék használatáról](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a Azure Functionshöz kapcsolódó hálózati beállításokhoz. Használjon Azure Policy aliasokat a "Microsoft. Web" és a "Microsoft. Network" névterekben, hogy egyéni házirendeket hozzon létre a Azure Functions hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a Azure Functions beépített szabályzat-definícióját is, például:

- A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a függvény alkalmazásaihoz

- A Function alkalmazás csak HTTPS protokollon keresztül érhető el

- A legújabb TLS-verziót kell használni a Function alkalmazásban

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagy léptékű Azure-környezeteket a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek egyetlen terv szerinti definícióban való kicsomagolásával. A tervet egyszerűen alkalmazhatja új előfizetésekre, környezetekre, valamint a verziószámozáson keresztül történő finomhangolásra és felügyeletre.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutatás**: Ha hálózati biztonsági csoportot használ a Azure functions implementációval, a NSG és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz használjon címkéket. Az egyes hálózati biztonsági csoportokra vonatkozó szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot, és így tovább, minden olyan szabály esetében, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti a hálózati erőforrás-konfigurációkat, és felderítheti a Azure functions üzemelő példányokhoz kapcsolódó hálózati beállítások és erőforrások változásait. Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor aktiválódnak, ha a kritikus hálózati beállítások vagy erőforrások változása zajlik. 

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

A Azure Functions az Azure Application Insights beépített integrációját is biztosítja a függvények figyeléséhez. Application Insights gyűjti a napló-, a teljesítmény-és a hiba adatait. A szolgáltatás automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és a függvények használatának megismerésében.

Ha beépített egyéni biztonsági/naplózási naplózást végez a Function alkalmazásban, engedélyezze a "FunctionAppLogs" diagnosztikai beállítást, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. 

Opcionálisan engedélyezheti és elvégezheti a fedélzeti adatokat az Azure Sentinel vagy egy harmadik féltől származó Rendszerinformáció és rendezvényszervezés megoldás számára. 

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](/azure/azure-monitor/platform/activity-log)

- [Azure Functions beállítása az Azure Application Insights](functions-monitoring.md)

- [Diagnosztikai beállítások (felhasználó által generált naplók) engedélyezése Azure Functions számára](functions-monitor-log-analytics.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

Ha beépített egyéni biztonsági/naplózási naplózást végez a Function alkalmazásban, engedélyezze a "FunctionAppLogs" diagnosztikai beállítást, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. 

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](/azure/azure-monitor/platform/activity-log)

- [Diagnosztikai beállítások (felhasználó által generált naplók) engedélyezése Azure Functions számára](functions-monitor-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitorban a szervezet megfelelőségi előírásai szerint állítsa be a függvények alkalmazásaihoz társított log Analytics-munkaterületek naplózásának megőrzési időtartamát.

- [Napló-megőrzési paraméterek beállítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak, valamint a Function alkalmazás diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján.

Engedélyezze Application Insights a Function apps számára a naplók, a teljesítmény és a hiba adatainak gyűjtéséhez. A Azure Portalon belül Application Insights gyűjtött telemetria-adatokat tekintheti meg.

Ha beépített egyéni biztonsági/naplózási naplózást végez a Function alkalmazásban, engedélyezze a "FunctionAppLogs" diagnosztikai beállítást, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. 

Opcionálisan engedélyezheti és elvégezheti a fedélzeti adatokat az Azure Sentinel vagy egy harmadik féltől származó Rendszerinformáció és rendezvényszervezés megoldás számára.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](/azure/azure-monitor/platform/activity-log)

- [A Azure Functions diagnosztikai beállításainak engedélyezése](functions-monitor-log-analytics.md)

- [A Azure Functions beállítása az Azure Application Insights és a telemetria-adatkezelés megtekintése](functions-monitoring.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak, valamint a Function alkalmazás diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján. A Log Analytics munkaterület-lekérdezések alapján létrehozhat riasztásokat.

Engedélyezze Application Insights a Function apps számára a naplók, a teljesítmény és a hiba adatainak gyűjtéséhez. Megtekintheti Application Insights által gyűjtött telemetria-adatokat, és riasztásokat hozhat létre a Azure Portalon belül.

Opcionálisan engedélyezheti és elvégezheti a fedélzeti adatokat az Azure Sentinel vagy egy harmadik féltől származó Rendszerinformáció és rendezvényszervezés megoldás számára.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](/azure/azure-monitor/platform/activity-log)

- [A Azure Functions diagnosztikai beállításainak engedélyezése](functions-monitor-log-analytics.md)

- [A Azure Functions Application Insights engedélyezése](https://docs.microsoft.com/azure/azure-functions/configure-monitoring#enable-application-insights-integration)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Azure Functionshoz való hozzáférés szabályozása Azure Active Directory (Azure ad) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az adatsíkok hozzáférése több módon is szabályozható, beleértve az engedélyezési kulcsokat, a hálózati korlátozásokat és az Azure AD-identitások érvényességét. Az engedélyezési kulcsokat a Azure Functions HTTP-végpontokhoz csatlakozó ügyfelek használják, és bármikor újra létrehozhatók. Ezek a kulcsok alapértelmezés szerint jönnek létre az új HTTP-végpontokhoz.

Több üzembe helyezési módszer is elérhető az alkalmazások működéséhez, amelyek némelyike generált hitelesítő adatok készletét is kihasználhatja. Tekintse át az alkalmazáshoz használt üzembe helyezési módszereket.

- [HTTP-végpont biztonságossá tétele](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Engedélyezési kulcsok beszerzése és újragenerálása](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#obtaining-keys)

- [Üzembe helyezési technológiák Azure Functions](functions-deployment-technologies.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [A Azure Security Center használata az identitás és a hozzáférés figyeléséhez ](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: ha lehetséges, használja a Azure Active Directory (Azure ad) SSO-t ahelyett, hogy egyéni, különálló hitelesítő adatokat konfiguráljon a Function alkalmazáshoz való hozzáféréshez. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata. A functions-alkalmazások egyszeri bejelentkezésének megvalósítása a App Service hitelesítés/engedélyezés funkció használatával.

- [A hitelesítés és az engedélyezés ismertetése Azure Functions](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#identity-providers)

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

**Útmutató**: az Azure-erőforrások bejelentkezéséhez és konfigurálásához konfigurált, többtényezős hitelesítéssel rendelkező emelt szintű hozzáférésű munkaállomások (Paw) használata. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést a Azure Portalhoz az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: az Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként a Function apps alkalmazásokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [A Function app konfigurálása az Azure AD-bejelentkezés használatára](../app-service/configure-authentication-provider-aad.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: az Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként a Function apps alkalmazásokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Hozzáférése van az Azure AD bejelentkezési tevékenységeihez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik az Azure Sentinel vagy egy harmadik féltől származó SIEM integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics belül is konfigurálhatja a kívánt naplózási riasztásokat.

- [A Function app konfigurálása az Azure AD-bejelentkezés használatára](../app-service/configure-authentication-provider-aad.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként a Function apps alkalmazásokhoz. A vezérlési síkon (a Azure Portal) való bejelentkezéshez használja a Azure AD Identity Protection és a kockázati észlelési funkciókat a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszok konfigurálásához. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. A Function apps-alkalmazásokat a virtuális hálózat (VNet)/subnet kell elválasztani, és megfelelően címkézve.

A hálózati elkülönítés végrehajtásához privát végpontokat is használhat. Az Azure Private-végpontok olyan hálózati adapterek, amelyek az Azure privát kapcsolaton keresztül csatlakoznak egy szolgáltatáshoz (például: Function app HTTPs-végpont). A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így lényegében bekapcsolja a szolgáltatást a virtuális hálózatba. A prémium és App Service csomagokban üzemeltetett függvények saját végpontokat is használhatnak.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)

- [Prémium csomag Azure Functions](functions-premium-plan.md)

- [Privát végpont ismertetése](../private-link/private-endpoint-overview.md)

- [Privát végpontok használata Azure Functionshoz](../app-service/networking/private-endpoint.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a function apps Azure Portal a "platform szolgáltatások: HÁLÓZATKEZELÉS: SSL" területen engedélyezze a "csak https" beállítást, és állítsa be a minimális TLS-verziót 1,2-re.

- [HTTPS megkövetelése a Function-alkalmazásokban](https://docs.microsoft.com/azure/azure-functions/security-concepts#require-https)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: jelenleg nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók jelenleg nem érhetők el Azure Functions. Az olyan alkalmazások címkézése, amelyek a bizalmas adatokat feldolgozzák, és ha a megfelelőség szempontjából szükséges, harmadik féltől származó megoldást is megvalósítanak.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával kezelheti a Function app Control Plane (a Azure Portal) elérését.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

A Microsoft kezeli a Azure Functions alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Function app létrehozásakor létre kell hoznia vagy hivatkoznia kell egy általános célú Azure Storage-fiókra, amely támogatja a blob, a üzenetsor és a Table Storage használatát. Ennek az az oka, hogy a függvények az Azure Storage-on alapulnak olyan műveletekre, mint például az eseményindítók és a naplózási függvények végrehajtásának kezelése. Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az ügyfél által felügyelt kulcsokat a blobok és a fájlok titkosításához. Ezeknek a kulcsoknak jelen kell lennie Azure Key Vaultban, hogy a Function alkalmazás hozzáférhessen a Storage-fiókhoz.

- [A Azure Functions tárolási szempontjainak ismertetése](storage-considerations.md)

- [Az Azure Storage-titkosítás megismerése a REST-adatok esetében](../storage/common/storage-service-encryption.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a termelési funkció alkalmazásaiban, valamint egyéb kritikus vagy kapcsolódó erőforrásokban is érvényben vannak.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: hozzon létre egy DevSecOps, amely biztosítja, hogy a függvény alkalmazásai biztonságosak legyenek, és a lehető legbiztonságosabbak maradjanak életciklusuk időtartama alatt. A DevSecOps beépíti a szervezete biztonsági csapatát és képességeiket a DevOps-gyakorlatba, így a csapaton belül mindenki felelős a biztonságért.

Emellett a Azure Security Center javaslatainak követésével biztonságosabbá teheti a Function-alkalmazásokat.

- [Folyamatos biztonsági ellenőrzés hozzáadása a CI/CD-folyamathoz](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&amp;preserve-view=true)

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Microsoft a biztonsági rések kezelését a Azure functions támogató mögöttes rendszereken hajtja végre, azonban a javaslatok súlyosságát Azure Security Center, valamint a biztonságos pontszámot is használhatja a környezeten belüli kockázat méréséhez. A biztonságos pontszám azon alapul, hogy hány Security Center javasolt javaslat. Az első megoldáshoz szükséges javaslatok rangsorolása érdekében vegye figyelembe az egyes problémák súlyosságát.

- [Biztonsági javaslatok hivatkozási útmutatója](../security-center/recommendations-reference.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: az Azure-erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézést, a felügyeleti csoportokat és a különálló előfizetéseket, ahol szükséges. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: jóváhagyott Azure-erőforrások és jóváhagyott szoftverek definiálása számítási erőforrásokhoz.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára. 

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva. 

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

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

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

**Útmutató**: a Function alkalmazás szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Web" névtérben, hogy egyéni szabályzatokat hozzon létre a functions-alkalmazások konfigurációjának naplózásához vagy érvényesítéséhez. Olyan beépített szabályzat-definíciókat is használhat, mint például a következők:

- A felügyelt identitást a Function alkalmazásban kell használni

- A távoli hibakeresést ki kell kapcsolni a Function apps szolgáltatásban

- a Function alkalmazás csak HTTPS protokollon keresztül érhető el

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés, ha nem létezik] az Azure-erőforrások biztonságos beállításainak betartatására használható.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ARM-sablonok és egyéni Azure-szabályzat-definíciók biztonságos tárolása és kezelése a verziókövetés számára.

- [Mi az infrastruktúra mint kód](/azure/devops/learn/what-is-infrastructure-as-code)

- [Szabályzat tervezése kód-munkafolyamatként](../governance/policy/concepts/policy-as-code.md)

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: beépített Azure Policy definíciók és Azure Policy aliasok használata a "Microsoft. Web" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: beépített Azure Policy definíciók és Azure Policy aliasok használata a "Microsoft. Web" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Az Azure Policy [audit], [megtagadás] és [telepítés ha nem létezik] használatával automatikusan kényszerítheti ki az Azure-erőforrások konfigurációit.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: felügyelt identitások használata a Azure Key Vaultkel együtt a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében. A felügyelt identitások lehetővé teszik a Function app számára, hogy hitelesítse magát bármely olyan szolgáltatáson, amely támogatja a Azure Active Directory (Azure AD) hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Key Vault létrehozása](../key-vault/secrets/quick-create-portal.md)

- [Felügyelt identitások használata App Service és Azure Functions](../app-service/overview-managed-identity.md)

- [Hitelesítés Key Vault](../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

- [Key Vault referenciák használata App Service és Azure Functions](../app-service/app-service-key-vault-references.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használata a Function alkalmazás automatikus felügyelt identitással való biztosításához Azure Active Directory (Azure ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitások használata App Service és Azure Functions](../app-service/overview-managed-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: az alkalmazás rendszeres biztonsági mentésének ütemezéséhez használja a Backup és a Restore funkciót. A prémium szintű csomagban futó functions-alkalmazások ugyanazokat az üzemeltetési funkciókat rendelkeznek, mint a Azure App Service webalkalmazásai, beleértve a "biztonsági mentés és visszaállítás" funkciót is.

Olyan verziókövetés-megoldást is használhat, mint például az Azure Repos és az Azure DevOps a kód biztonságos tárolásához és kezeléséhez. Az Azure DevOps Services számos Azure Storage-funkciót használ, hogy gondoskodni tudjon az adatok rendelkezésre állásáról hardverhiba, szolgáltatáskimaradás vagy régiós katasztrófa esetén. Az Azure DevOps csapata olyan eljárásokat is követ, amelyek a véletlen vagy rosszindulatú törlésektől védi az adatokat.

- [Adatok biztonsági mentése az Azure-ban](../app-service/manage-backup.md)

- [Az adatelérhetőség ismertetése az Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability&amp;preserve-view=true)

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: az alkalmazás rendszeres biztonsági mentésének ütemezéséhez használja a Backup és a Restore funkciót. A prémium szintű csomagban futó functions-alkalmazások ugyanazokat az üzemeltetési funkciókat rendelkeznek, mint a Azure App Service webalkalmazásai, beleértve a "biztonsági mentés és visszaállítás" funkciót is. Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül.

Olyan verziókövetés-megoldást is használhat, mint például az Azure Repos és az Azure DevOps a kód biztonságos tárolásához és kezeléséhez. Az Azure DevOps Services számos Azure Storage-funkciót használ, hogy gondoskodni tudjon az adatok rendelkezésre állásáról hardverhiba, szolgáltatáskimaradás vagy régiós katasztrófa esetén. Az Azure DevOps csapata olyan eljárásokat is követ, amelyek a véletlen vagy rosszindulatú törlésektől védi az adatokat.

- [Adatok biztonsági mentése az Azure-ban](../app-service/manage-backup.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Az adatelérhetőség ismertetése az Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability&amp;preserve-view=true)

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Ellenőrizze, hogy a biztonsági mentés és visszaállítás szolgáltatás rendszeres időközönként hogyan végezheti el a visszaállítást. Ha egy másik offline helyet használ a kód biztonsági mentéséhez, rendszeres időközönként gondoskodjon a teljes helyreállítások végrehajtásáról. Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását.

- [Azure-beli alkalmazás visszaállítása biztonsági másolatból](../app-service/web-sites-restore.md)

- [Azure-beli alkalmazás visszaállítása pillanatképből](../app-service/app-service-web-restore-snapshots.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a biztonsági mentés és visszaállítás funkció biztonsági mentései egy Azure Storage-fiókot használnak az előfizetésében. Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az ügyfél által felügyelt kulcsokat a tárolási adat titkosításához.

Ha ügyfél által felügyelt kulcsokat használ, győződjön meg arról, hogy a Key Vaultban Soft-Delete engedélyezve van a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemhez.

- [Azure Storage-titkosítás inaktív állapotban](../storage/common/storage-service-encryption.md)

- [A Soft-Delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [A NIST számítógép-biztonsági incidensek kezelési útmutatója](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Megosztott

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

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a biztonsági riasztásokra és a Logic Appsokkal kapcsolatos ajánlásokra adott válaszokat.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
