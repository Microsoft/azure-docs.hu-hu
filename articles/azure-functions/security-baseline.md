---
title: Az Azure biztonsági alapkonfigurációja Azure Functions
description: A Azure Functions alapkonfigurációja eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 90ae774a4e243b854758c1c7a6cc10a882cd584f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831077"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Az Azure biztonsági alapkonfigurációja Azure Functions

Ez a biztonsági alapkonfiguráció az Azure biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Azure Functions. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az azure-beli biztonsági Azure Functions. **A** Azure Functions nem alkalmazható vezérlők ki vannak zárva.

 
Ha meg Azure Functions az Azure-biztonsági teljesítményteszt teljes körű leképezését, tekintse meg a teljes Azure Functions alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Integrálhatja Azure Functions alkalmazásait egy Azure-beli virtuális hálózattal. A prémium szintű csomagon futó függvényalkalmazások ugyanazokkal az üzemeltetési képességekkel rendelkeznek, mint a Azure App Service, amely tartalmazza a "VNet-integráció" funkciót.  Az Azure-beli virtuális hálózatok lehetővé teszik, hogy számos Azure-erőforrást ( például Azure Functions) egy nem internetes átirányítható hálózatba helyezzen.

- [Functions integrálása Azure-beli Virtual Network](functions-create-vnet.md)

- [Virtuális hálózatok integrációjának Azure Functions és Azure App Service](../app-service/web-sites-integrate-with-vnet.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** A Azure Security Center és kövesse a hálózatvédelmi javaslatokat a hálózati erőforrások és a hálózati konfigurációk biztonságossá Azure Functions érdekében.

Ha hálózati biztonsági csoportokat használ a Azure Functions implementációval, engedélyezze a hálózati biztonsági csoportok forgalmi naplóit, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához. A hálózati biztonsági csoportok forgalmi naplóit egy Log Analytics-munkaterületre is elküldheti, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő adatforgalmába. A hálózati Traffic Analytics néhány előnye a hálózati tevékenység vizualizációja és a gyorspontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati helytelen konfigurációk azonosítása.

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** A Azure Functions végpontok teljes biztonságának biztosítása érdekében érdemes lehet az alábbi függvényalkalmazásszintű biztonsági beállítások egyikét bevetni:

- A App Service hitelesítés vagy engedélyezés engedélyezése bekapcsolva

- Kérelmek hitelesítése az Azure API Management (APIM) használatával

- A függvényalkalmazás üzembe helyezése egy Azure App Service Environment.

Emellett győződjön meg arról, hogy a távoli hibakeresés le van tiltva az éles környezetben Azure Functions. Emellett az eredetközi erőforrás-megosztás (CORS) nem engedélyezheti az összes tartomány számára a függvényalkalmazás azure-beli hozzáférését. Csak a szükséges tartományok számára engedélyezze a függvényalkalmazással való interakciót.

Fontolja meg a Azure Web Application Firewall (WAF) üzembe helyezését a hálózati konfiguráció részeként a bejövő forgalom további vizsgálatához. Engedélyezze a WAF diagnosztikai beállítását, és a naplókat egy tárfiókba, eseményközpontba vagy Log Analytics-munkaterületre. 

- [A végpontok Azure Functions biztonságossá teve éles környezetben](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Engedélyezze DDoS Protection Standard szabványt a függvényalkalmazásokkal társított virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az Integrated (Integrált) Azure Security Center veszélyforrás-felderítési funkcióival megtagadhatja az ismert rosszindulatú vagy nem használt nyilvános IP-címekkel való kommunikációt.

Emellett konfigurálnia kell egy előoldali átjárót, például a Azure Web Application Firewall, hogy hitelesítse az összes bejövő kérést, és kiszűrje a rosszindulatú forgalmat. Azure Web Application Firewall az SQL-injektációk, a webhelyek közötti szkriptek, a kártevőfeltöltések és a DDoS-támadások blokkolására irányuló bejövő webes forgalom vizsgálatán keresztül segítheti a függvényalkalmazás biztonságát. A bevezetéshez Web Application Firewall privát végpontok App Service Environment vagy használata szükséges. Privát végpontokat a Prémium és a Prémium szintű előfizetési csomagokban üzemeltetett App Service használhat.

- [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Az Azure Security Center fenyegetésintelligencia](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Ha hálózati biztonsági csoportot használ a Azure Functions implementációval, engedélyezze a hálózati biztonsági csoport forgalmi naplóit, és küldje el a naplókat egy tárfiókba a forgalom naplózásához. Folyamatnaplókat is küldhet egy Log Analytics-munkaterületre, és Traffic Analytics segítségével betekintést nyerhet az Azure-felhő forgalomába. A hálózati Traffic Analytics néhány előnye a hálózati tevékenység vizualizációja és a gyorspontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati helytelen konfigurációk azonosítása.

- [Hálózati biztonsági csoport forgalomnaplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Az Network Watcher](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Konfigurálni kell egy előoldali átjárót, például Azure Web Application Firewall a bejövő kérések hitelesítéséhez és a kártékony forgalom kiszűréséhez. Azure Web Application Firewall a függvényalkalmazások biztonságát azáltal, hogy megvizsgálja a bejövő webes forgalmat, és blokkolja az SQL-injektciókat, a webhelyek közötti szkripteket, a kártevőfeltöltéseket és a DDoS-támadásokat. A bevezetéshez Web Application Firewall privát végpontok App Service Environment vagy használatára van szükség. Privát végpontokat használhat a Prémium és prémium szintű előfizetési csomagokban üzemeltetett App Service számára.

Alternatív megoldásként a Barracuda Web Application Firewall for Azure piactér is elérhető a Azure Marketplace behatolásészlelési vagy -megelőzési funkciókkal.

- [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)

- [Azure Functions Prémium szintű csomag](functions-premium-plan.md)

- [Az App Service Environment bemutatása](../app-service/environment/intro.md)

- [App Service-környezet hálózati megfontolásai](../app-service/environment/network-info.md) 

- [A Azure Web Application Firewall](../web-application-firewall/overview.md)

- [Privát végpontok használata a Azure Functions](../app-service/networking/private-endpoint.md)

- [A Barracuda WAF Cloud Service](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** Állítson be egy előoldali átjárót a hálózathoz, például Azure Web Application Firewall végpontok között TLS-titkosítással. A bevezetéshez Web Application Firewall privát végpontok App Service Environment vagy használatára van szükség. Privát végpontokat használhat a Prémium és prémium szintű előfizetési csomagokban üzemeltetett App Service számára.

- [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)

- [Azure Functions Prémium csomag](functions-premium-plan.md)

- [Az App Service Environment bemutatása](../app-service/environment/intro.md)

- [App Service-környezet hálózati megfontolásai](../app-service/environment/network-info.md) 

- [A Azure Web Application Firewall](../web-application-firewall/overview.md)

- [Végpontok között elérhető TLS konfigurálása a Application Gateway használatával](../application-gateway/end-to-end-ssl-portal.md)

- [Privát végpontok használata Azure Functions](../app-service/networking/private-endpoint.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Virtual Network szolgáltatáscímkékkel hálózati hozzáférés-vezérlést határozhat meg a hálózati biztonsági csoportok vagy Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha megadja a szolgáltatáscímke nevét (például AzureAppService) egy szabály megfelelő forrás- vagy célmezőben, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkéket a címek változásával.

- [További információ a szolgáltatáscímkék használatával kapcsolatban](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Szabványos biztonsági konfigurációk meghatározása és implementációja a Azure Functions. A Azure Policy a "Microsoft.Web" és a "Microsoft.Network" névterek aliasai segítségével egyéni szabályzatokat hozhat létre az adatbázis hálózati konfigurációjának naplózásához Azure Functions. Emellett beépített szabályzatdefiníciókat is használhat a Azure Functions, például:

- A CORS nem engedélyezheti minden erőforrás számára a függvényalkalmazások hozzáférését

- A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie

- A függvényalkalmazásban a legújabb TLS-verziót kell használni

Az Azure Blueprints használatával leegyszerűsítheti a nagy méretű Azure-beli üzembe helyezéseket, mivel egyetlen tervdefinícióba csomagolásával fontos környezeti összetevők, például Azure Resource Manager-sablonok, Azure-beli szerepköralapú hozzáférés-vezérlés (Azure RBAC) és szabályzatok vannak. Könnyedén alkalmazhatja a tervet az új előfizetésekhez, környezetekhez, és finomhangolhatja a vezérlést és a felügyeletet a verziószámozás segítségével.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints-terv létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Ha hálózati biztonsági csoportot használ a Azure Functions implementációval, használjon címkéket az NSG-khez és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. Az egyes hálózati biztonsági csoportok szabályaihoz a "Leírás" mezőben adhatja meg az üzleti igényeket és/vagy időtartamot stb. a hálózatra/hálózatról be- és vissza forgalmat engedélyező szabályokhoz.

A címkézéshez kapcsolódó beépített Azure-szabályzatdefiníciók (például "Címke és érték megkövetelése" ) használatával biztosíthatja, hogy az összes erőforrás címkékkel legyen létrehozva, és értesítést küld a meglévő, címkézetlen erőforrásokról.

A címkék alapján Azure PowerShell azure cli használatával erőforrásokat kereshet vagy hajthat végre műveleteken.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnaplóval monitorozhatja a hálózati erőforrások konfigurációit, és észlelheti a hálózati beállítások és az erőforrások változásait a Azure Functions üzemelő példányainál. Hozzon létre riasztásokat a Azure Monitor, amelyek a kritikus hálózati beállítások vagy erőforrások módosításakor aktiválódnak. 

- [Azure-tevékenységnapló eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** A vezérlősík naplózásához engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre, egy Azure-eseményközpontba vagy egy Azure-tárfiókba archiválás céljából. Az Azure-tevékenységnapló adataival meghatározhatja az Azure-erőforrások vezérlősíkszintén végrehajtott írási műveletek (PUT, POST, DELETE) "mit, ki és mikor" műveletét.

Azure Functions beépített Azure-integrációt is kínál Application Insights a függvények figyelése érdekében. Application Insights napló-, teljesítmény- és hibaadatokat gyűjt. Automatikusan észleli a teljesítményanomáliákat, és hatékony elemzési eszközöket tartalmaz, amelyek segítenek diagnosztizálni a problémákat, és megérteni a függvények működését.

Ha beépített egyéni biztonsági/naplózási naplózással rendelkezik a függvényalkalmazásban, engedélyezze a "FunctionAppLogs" diagnosztikai beállítást, és küldje el a naplókat egy Log Analytics-munkaterületre, azure-eseményközpontba vagy Azure Storage-fiókba archiválásra. 

Igény szerint engedélyezheti és bevetheti az adatokat egy Azure Sentinel külső rendszerinformáció- és eseménykezelési megoldásba. 

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

- [Az Azure Azure Functions beállítása Application Insights](functions-monitoring.md)

- [Diagnosztikai beállítások (felhasználó által létrehozott naplók) engedélyezése a Azure Functions](functions-monitor-log-analytics.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** A vezérlősík naplózásához engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre, egy Azure-eseményközpontba vagy egy Azure Storage-fiókba archiválás céljából. Az Azure-tevékenységnapló adataival meghatározhatja az Azure-erőforrások vezérlősíkszintén végrehajtott írási műveletek (PUT, POST, DELETE) "mit, ki és mikor" műveletét.

Ha beépített egyéni biztonsági/naplózási naplózással rendelkezik a függvényalkalmazásban, engedélyezze a "FunctionAppLogs" diagnosztikai beállítást, és küldje el a naplókat egy Log Analytics-munkaterületre, azure-eseményközpontba vagy Azure Storage-fiókba archiválásra. 

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

- [Diagnosztikai beállítások (felhasználó által létrehozott naplók) engedélyezése a Azure Functions](functions-monitor-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése operációs rendszerekről

**Útmutató:** Nem alkalmazható; ez az útmutató az IaaS számítási erőforrásokhoz való.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági naplók tárolásának megőrzésének konfigurálása

**Útmutató:** A Azure Monitor meg a függvényalkalmazásokkal társított Log Analytics-munkaterületek naplómegőrzési időszakát a szervezet megfelelőségi szabályozásának megfelelően.

- [Naplómegőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, valamint a függvényalkalmazás diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre. Lekérdezéseket hajthat végre a Log Analyticsben a kifejezések kereséséhez, trendek azonosításához, minták elemzéséhez és számos további elemzéshez az összegyűjtött adatok alapján.

Engedélyezze Application Insights, hogy a függvényalkalmazások napló-, teljesítmény- és hibaadatokat gyűjtsenek. A rendszer által gyűjtött telemetriai adatokat Application Insights a Azure Portal.

Ha beépített egyéni biztonsági/auditnaplózási funkcióval rendelkezik a függvényalkalmazásban, engedélyezze a "FunctionAppLogs" diagnosztikai beállítást, és küldje el a naplókat egy Log Analytics-munkaterületre, egy Azure-eseményközpontba vagy egy Azure-tárfiókba archiválás céljából. 

Igény szerint engedélyezheti és be is adatokat lehet Azure Sentinel egy külső rendszerinformáció- és eseménykezelési megoldásba.

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

- [Diagnosztikai beállítások engedélyezése a Azure Functions](functions-monitor-log-analytics.md)

- [Az Azure Azure Functions és Application Insights telemetriai adatok megtekintése](functions-monitoring.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, valamint a függvényalkalmazás diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre. Lekérdezések végrehajtása a Log Analyticsben a kifejezések kereséséhez, trendek azonosításához, minták elemzéséhez, valamint az összegyűjtött adatokon alapuló számos további elemzéshez. Riasztásokat a Log Analytics-munkaterület lekérdezései alapján hozhat létre.

Engedélyezze Application Insights, hogy a függvényalkalmazások napló-, teljesítmény- és hibaadatokat gyűjtsenek. Megtekintheti a riasztások által gyűjtött telemetriai adatokat, Application Insights riasztásokat hozhat létre a Azure Portal.

Igény szerint engedélyezheti és bevetheti az adatokat egy Azure Sentinel külső rendszerinformáció- és eseménykezelési megoldásba.

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

- [Diagnosztikai beállítások engedélyezése a Azure Functions](functions-monitor-log-analytics.md)

- [A Application Insights engedélyezése Azure Functions](./configure-monitoring.md#enable-application-insights-integration)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Azure Active Directory (Azure AD) beépített szerepkörökkel rendelkezik, amelyekhez explicit módon kell hozzárendelni és lekérdezhetőnek kell lennie. Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajt végre a rendszergazdai csoportok tagjainak felderítéséhez.

- [Címtárszerepkomó lekért szerepkör az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** A vezérlősík hozzáférése Azure Functions vezérlése Azure Active Directory (Azure AD) használatával történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az adatsíkhoz való hozzáférés többféle módon szabályozható, beleértve az engedélyezési kulcsokat, a hálózati korlátozásokat és az Azure AD-identitások érvényességének érvényességét. Az engedélyezési kulcsokat a http Azure Functions végpontjaihoz csatlakozó ügyfelek használják, és bármikor újragenerálhatóak. Ezek a kulcsok alapértelmezés szerint új HTTP-végpontok számára jönnek létre.

A függvényalkalmazások többféle üzembe helyezési módszerrel is használhatók, amelyek némelyike a létrehozott hitelesítő adatok készletét használhatja. Tekintse át az alkalmazáshoz használt üzembe helyezési módszereket.

- [HTTP-végpont biztonságossá tere](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Engedélyezési kulcsok beszerzése és újragenerálása](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Üzembe helyezési technológiák a Azure Functions](functions-deployment-technologies.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban. A Azure Security Center és hozzáférés-kezelés használatával figyelheti a rendszergazdai fiókok számát.

Emellett a dedikált rendszergazdai fiókok nyomon követéséhez használhatja a Azure Security Center vagy beépített Azure-szabályzatok javaslatait, például:

- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

További információk a hivatkozott hivatkozásokon érhetők el.

- [A Azure Security Center identitás és hozzáférés figyelése ](../security-center/security-center-identity-access.md)

- [A Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Amikor csak lehetséges, használjon Azure Active Directory (Azure AD) SSO-t ahelyett, hogy különálló hitelesítő adatokat konfigurál a függvényalkalmazáshoz való adateléréshez. Használja Azure Security Center Identity and Access Management (Identitás- és hozzáférés-kezelés) javaslatokat. Implementálja az egyszeri bejelentkezést a függvényalkalmazások számára az App Service hitelesítés/engedélyezés funkcióval.

- [A hitelesítés és az engedélyezés Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Az SSO és az Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center identitás- és hozzáférés-kezelésre vonatkozó javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomások (PAW) használata többtényezős hitelesítéssel, konfigurálva az Azure-erőforrásokba való bejelentkezéshez és konfiguráláshoz. 

- [Tudnivalók az emelt szintű hozzáférésű munkaállomásról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával naplókat és riasztásokat generál, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

Emellett az Azure AD kockázatészlelései segítségével megtekintheti a kockázatos felhasználói viselkedéssel kapcsolatos riasztásokat és jelentéseket.

- [A Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázatészlelései](../active-directory/identity-protection/overview-identity-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés elnevezett helyeinek használatával engedélyezheti a hozzáférést a Azure Portal IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításaiból.

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata a függvényalkalmazások központi hitelesítési és engedélyezési rendszereként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt. Az Azure AD emellett a felhasználói hitelesítő adatokat is megsokossa, hashes és biztonságosan tárolja.

- [A függvényalkalmazás konfigurálása az Azure AD-bejelentkezés használatára](../app-service/configure-authentication-provider-aad.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férnek hozzá.

- [Az Azure AD-jelentéskészítés](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity Access-felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Azure Active Directory (Azure AD) a függvényalkalmazások központi hitelesítési és engedélyezési rendszereként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt. Az Azure AD emellett a felhasználói hitelesítő adatokat is megsóz, hashekkel és biztonságosan tárolja.

Hozzáféréssel rendelkezik az Azure AD bejelentkezési tevékenységéhez, naplózási és kockázati eseménynapló-forrásaihoz, amelyek lehetővé teszik az Azure Sentinel vagy külső SIEM integrálását.

Leegyszerűsítheti ezt a folyamatot, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókokhoz, és elküldi az auditnaplókat és a bejelentkezési naplókat egy Log Analytics-munkaterületre. A kívánt naplóriasztásokat a Log Analyticsben konfigurálhatja.

- [A függvényalkalmazás konfigurálása az Azure AD-bejelentkezés használatára](../app-service/configure-authentication-provider-aad.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Az Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató:** Azure Active Directory (Azure AD) a függvényalkalmazások központi hitelesítési és engedélyezési rendszereként. A fiók bejelentkezési viselkedésének eltérései a vezérlősíkon (Azure Portal) a Azure AD Identity Protection és kockázatészlelési funkciók használatával konfigurálhatja az automatikus válaszokat a felhasználói identitásokkal kapcsolatos gyanús műveletekre. Az adatokat be is Azure Sentinel vizsgálathoz.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrásokat.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Különálló előfizetéseket és/vagy felügyeleti csoportokat implementálja a fejlesztéshez, teszteléshez és éles környezethez. A függvényalkalmazásokat virtuális hálózattal (VNet)/alhálózattal kell elválasztani, és megfelelően kell címkézni.

Privát végpontokat is használhat a hálózat elkülönítésének végrehajtásához. Az Azure privát végpontok olyan hálózati adapterek, amelyek privát módon és biztonságosan csatlakoztatják Egy szolgáltatáshoz (például a függvényalkalmazás HTTPs-végpontja) a Azure Private Link. A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így lényegében bekapcsolja a szolgáltatást a virtuális hálózatba. Privát végpontokat használhat a Prémium és prémium szintű előfizetési csomagokban üzemeltetett App Service számára.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Új Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)

- [Azure Functions Prémium szintű csomag](functions-premium-plan.md)

- [A privát végpontok](../private-link/private-endpoint-overview.md)

- [Privát végpontok használata a Azure Functions](../app-service/networking/private-endpoint.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** A függvényalkalmazások Azure Portal-ben a "Platformfunkciók: Hálózat: SSL" alatt engedélyezze a "Csak HTTPs" beállítást, és állítsa a minimális TLS-verziót 1.2-re.

- [HTTPS megkövetelve a függvényalkalmazásokban](./security-concepts.md#require-https)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: A bizalmas adatok azonosítása aktív felderítési eszközzel

**Útmutató:** Jelenleg nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók jelenleg nem érhetők el Azure Functions. Címkézheti azokat a függvényalkalmazásokat, amelyek bizalmas adatokat feldolgozhatnak, és megfelelőségi célokból megvalósíthatnak külső megoldásokat.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft bizalmasként kezeli az összes ügyféltartalmat, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozása érdekében

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata a függvényalkalmazás-vezérlősíkhoz (a virtuális géphez) való hozzáférés Azure Portal.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató:** Nem alkalmazható; Ez a javaslat IaaS számítási erőforrásokhoz való.

A Microsoft kezeli a mögöttes infrastruktúra Azure Functions és szigorú vezérlőket valósított meg az ügyféladatok elvesztésének vagy kitettségének megakadályozására.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Függvényalkalmazás létrehozásakor létre kell hoznia egy általános célú Azure Storage-fiókot, amely támogatja a Blob, Queue és Table Storage szolgáltatásokat. Ennek az az oka, hogy a Functions az Azure Storage-ban kezeli az olyan műveleteket, mint az eseményindítók kezelése és a függvények végrehajtásának naplózása. Az Azure Storage egy tárfiókban tárolt összes adatot titkosítja. Alapértelmezés szerint az adatok titkosítása Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához ügyfél által kezelt kulcsokat is meg lehet majd használni a blob- és fájladatok titkosításához. Ezeknek a kulcsoknak jelen kell Azure Key Vault, hogy a függvényalkalmazás hozzáfér a tárfiókhoz.

- [A tárolók tárolási szempontjainak Azure Functions](storage-considerations.md)

- [Az Azure Storage-titkosítás használata az adattároláshoz](../storage/common/storage-service-encryption.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Monitor azure-tevékenységnaplóval használva riasztásokat hozhat létre az éles függvényalkalmazások, valamint más kritikus vagy kapcsolódó erőforrások változásairól.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált sebezhetőség-ellenőrzési eszközök futtatása

**Útmutató:** DevSecOps-eljárás alkalmazása annak biztosításához, hogy a függvényalkalmazások biztonságosak és a lehető legbiztonságosabbak maradjanak az életciklusuk során. A DevSecOps beépíti a szervezet biztonsági csapatát és képességeit a DevOps-eljárásokba, így a biztonság a csapat minden tagja felelőssége lesz.

A függvényalkalmazások biztonságossá Azure Security Center kövesse az itt felsorolt javaslatokat.

- [Folyamatos biztonsági ellenőrzés hozzáadása a CI-/CD-folyamathoz](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Biztonsági rések felmérésére Azure Security Center javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** A Microsoft elvégzi a biztonsági rések kezelését a Azure Functions-t támogató mögöttes rendszereken, de a Azure Security Center-on belüli javaslatok súlyosságát és a biztonsági pontszámot is használhatja a kockázat mérésére a környezeten belül. A biztonsági pontszám azon alapul, hogy hány Security Center javaslatokat enyhített. A megoldáshoz szükséges javaslatok rangsorolása érdekében vegye figyelembe az egyes ajánlások súlyosságát.

- [Biztonsági javaslatok referencia-útmutatója](../security-center/recommendations-reference.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph az előfizetés(ek)beli összes erőforrás (pl. számítás, tárolás, hálózat, portok és protokollok stb.) lekérdezésére/felderítésére használható. Győződjön meg arról, hogy megfelelő (olvasási) engedélyeket ad meg a bérlőben, és számba veszi az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

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

**Útmutató:** Az Azure-erőforrások rendszerezéséhez és nyomon követéséhez használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket, ahol szükséges. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

Emellett az Azure Policy használatával korlátozásokat is érvényben hagyhat az ügyfél-előfizetés(ök)ben az alábbi beépített szabályzatdefiníciók használatával létrehozott erőforrások típusára:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További információk a hivatkozott hivatkozásokon érhetők el.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Új Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és leltározása

**Útmutató:** Jóváhagyott Azure-erőforrások és jóváhagyott szoftverek definiálása a számítási erőforrásokhoz.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy az előfizetésben vagy előfizetésben létrehozható erőforrások típusának korlátozására. 

A Azure Resource Graph erőforrások lekérdezésére/felderítésére az előfizetése(ek)en belül.  Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetés(ök)ben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További információk a hivatkozott hivatkozásokon érhetők el.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés konfigurálásával korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Standard biztonsági konfigurációkat definiálhat és valósíthat meg a függvényalkalmazáshoz a Azure Policy. A Azure Policy a "Microsoft.Web" névtérben található aliasok használatával egyéni szabályzatokat hozhat létre a függvényalkalmazások konfigurációjának naplózásához vagy kényszerítéséhez. Beépített szabályzatdefiníciókat is használhat, például:

- A felügyelt identitást a függvényalkalmazásban kell használni

- A távoli hibakeresést ki kell kapcsolva a függvényalkalmazások esetében

- A függvényalkalmazásnak csak HTTPS-protokollon keresztül szabad elérhetőnek lennie

További információk a hivatkozott hivatkozásokon érhetők el.

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** Az Azure Policy [megtagadás] és [üzembe helyezés, ha nem létezik] használatával kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** ARM-sablonok és egyéni Azure-szabályzatdefiníciók biztonságos tárolása és kezelése a forrásvezérlőben.

- [Mi az az infrastruktúra, mint kód?](/azure/devops/learn/what-is-infrastructure-as-code)

- [Szabályzat tervezése kód-munkafolyamatként](../governance/policy/concepts/policy-as-code.md)

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Beépített Azure Policy és Azure Policy a "Microsoft.Web" névtér aliasai segítségével egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és folyamatot a szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása az Azure-erőforrásokhoz

**Útmutató:** Beépített Azure Policy és Azure Policy a "Microsoft.Web" névtér aliasai segítségével egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Az Azure-erőforrások konfigurációjának automatikus kényszerítéséhez használja az Azure Policy [audit], [deny] és [deploy if not exist] (Azure-szabályzat [naplózás], [megtagadás] és [üzembe helyezés, ha nem létezik] beállítását.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** A Managed Identities és a Azure Key Vault segítségével leegyszerűsítheti és biztonságossá teheti a titkos adatok kezelését a felhőalkalmazások számára. A felügyelt identitások lehetővé teszik, hogy a függvényalkalmazás bármely olyan szolgáltatásban hitelesítsen, amely támogatja az Azure Active Directory- (Azure AD-) hitelesítést, beleértve a Key Vault-hitelesítést is, anélkül, hogy hitelesítő adatokat a kódban lenne.

- [Új Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Felügyelt identitások használata a App Service és Azure Functions](../app-service/overview-managed-identity.md)

- [Hitelesítés a Key Vault](../key-vault/general/authentication.md)

- [Új hozzáférési szabályzat Key Vault hozzárendelése](../key-vault/general/assign-access-policy-portal.md)

- [Hivatkozás Key Vault a App Service és a Azure Functions](../app-service/app-service-key-vault-references.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Felügyelt identitások használatával biztosítson egy automatikusan felügyelt identitást a függvényalkalmazás számára a Azure Active Directory (Azure AD) szolgáltatásban. A felügyelt identitások lehetővé teszik az Azure AD-hitelesítést támogató bármely szolgáltatásban történő hitelesítést, beleértve a Key Vault is, anélkül, hogy hitelesítő adatokat ad meg a kódban.

- [Felügyelt identitások használata a App Service és Azure Functions](../app-service/overview-managed-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódon belül. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** Az alkalmazás rendszeres biztonsági mentésének ütemezéséhez használja a Biztonsági mentés és visszaállítás szolgáltatást. A prémium szintű csomagon futó függvényalkalmazások ugyanazokkal az üzemeltetési képességekkel rendelkeznek, mint a Azure App Service, amely magában foglalja a "Biztonsági mentés és visszaállítás" funkciót.

A kód biztonságos tárolására és kezelésére olyan forrásvezérlési megoldást is használhat, mint az Azure Repos és az Azure DevOps. Az Azure DevOps Services számos Azure Storage-funkciót használ, hogy gondoskodni tudjon az adatok rendelkezésre állásáról hardverhiba, szolgáltatáskimaradás vagy régiós katasztrófa esetén. Az Azure DevOps csapata olyan eljárásokat is követ, amelyek a véletlen vagy rosszindulatú törlésektől védi az adatokat.

- [Adatok biztonsági mentése az Azure-ban](../app-service/manage-backup.md)

- [Az Adatok rendelkezésre állása az Azure DevOpsban](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és az ügyfelek által kezelt kulcsok biztonsági mentése

**Útmutató:** Az alkalmazás rendszeres biztonsági mentésének ütemezéséhez használja a Biztonsági mentés és visszaállítás szolgáltatást. A prémium szintű csomagon futó függvényalkalmazások ugyanazokkal az üzemeltetési képességekkel rendelkeznek, mint a Azure App Service, amely magában foglalja a "Biztonsági mentés és visszaállítás" funkciót. Ügyfél által felügyelt kulcsok biztonsági mentése a Azure Key Vault.

A kód biztonságos tárolására és kezelésére olyan forrásvezérlési megoldást is használhat, mint az Azure Repos és az Azure DevOps. Az Azure DevOps Services számos Azure Storage-funkciót használ, hogy gondoskodni tudjon az adatok rendelkezésre állásáról hardverhiba, szolgáltatáskimaradás vagy régiós katasztrófa esetén. Az Azure DevOps csapata olyan eljárásokat is követ, amelyek a véletlen vagy rosszindulatú törlésektől védi az adatokat.

- [Adatok biztonsági mentése az Azure-ban](../app-service/manage-backup.md)

- [Kulcstartókulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Az Adatok rendelkezésre állása az Azure DevOpsban](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Győződjön meg arról, hogy a biztonsági mentés és visszaállítás szolgáltatásból rendszeres visszaállítást végezhet. Ha egy másik offline helyet használ a kód biztonsági mentéséhez, rendszeres időközönként győződjön meg arról, hogy képes a teljes visszaállításra. Tesztelje az ügyfél által kezelt kulcsok biztonságimentett visszaállítását.

- [Alkalmazás visszaállítása az Azure-ban biztonsági másolatból](../app-service/web-sites-restore.md)

- [Alkalmazás visszaállítása pillanatképből az Azure-ban](../app-service/app-service-web-restore-snapshots.md)

- [Kulcstartókulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** A Biztonsági mentés és visszaállítás funkció biztonsági mentései az előfizetésben egy Azure Storage-fiókot használnak. Az Azure Storage a tárfiókban tárolt összes adatot titkosítja. Az adatok alapértelmezés szerint a Microsoft által kezelt kulcsokkal vannak titkosítva. A titkosítási kulcsok további szabályozása érdekében ügyfél által kezelt kulcsokat is meg lehet majd használni a tárolási adatok titkosításához.

Ha ügyfél által kezelt kulcsokat használ, győződjön meg arról, Soft-Delete a Key Vault a kulcsok véletlen vagy rosszindulatú törlés elleni védelme érdekében.

- [Azure Storage-titkosítás inaktív állapotban](../storage/common/storage-service-encryption.md)

- [Az Soft-Delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálások konfigurálása a Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [A NIST számítógépes biztonsági incidenskezelési útmutatója](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így fontossági sorrendbe adhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy Security Center mennyire biztos a riasztás kiadásához használt eredményben vagy az analyticiában, valamint az alapján, hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

Emellett egyértelműen jelöljük meg az előfizetéseket (például: éles környezetben, nem éles környezetben), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg az NIST kiadványát: Útmutató az IT-tervekhez és -képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha az Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy az ügyfél adatait illetéktelen vagy illetéktelen fél férte hozzá.  Tekintse át az incidenseket a probléma megoldása érdekében.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A Azure Security Center riasztások és javaslatok exportálása a Folyamatos exportálás funkcióval. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A munkafolyamat-automatizálási szolgáltatás Azure Security Center a biztonsági riasztásokhoz és javaslatokhoz adott válaszok automatikus aktiválásához a Logic Apps.

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft kötelezettségvállalási szabályait annak biztosításához, hogy a behatolási tesztek ne sértsen meg Microsoft-szabályzatokat. Használja a Microsoft red teaming és élő webhely-behatolási tesztek Microsoft által felügyelt felhőinfra infrastruktúrája, szolgáltatásai és alkalmazásai elleni stratégiáját és végrehajtását.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
