---
title: Az Azure Monitor dokumentációjának újdonságai
description: A Azure Monitor dokumentációjának frissítése havonta frissül.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: 35bfc9c752243a69912b0732c4105712fa4a6086
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377669"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Az Azure Monitor dokumentációjának újdonságai

Ez a cikk felsorolja Azure Monitor új vagy jelentős mértékben frissített cikkeket. A rendszer minden hónap első hetében frissíti az előző hónapból származó cikkek frissítéseinek befoglalásával.

## <a name="january-2021"></a>2021. január 

### <a name="general"></a>Általános 
- [Azure monitor GYIK](faq.md) – a Application Insights eszköz adataihoz hozzáadott bejegyzés.
### <a name="agents"></a>Ügynökök  
- [Windows esemény-nyomkövetés-(ETW-) események gyűjtése az analysis Azure monitor-naplókban](platform/data-sources-event-tracing-windows.md) – új cikk.
- [Adatgyűjtési szabályok a Azure monitor (előzetes verzió)](platform/data-collection-rule-overview.md) – a PowerShell-és CLI-mintákra mutató hivatkozásokkal bővült.

### <a name="alerts"></a>Riasztások  
- [Konfigurálja az Azure-t a ITSM-eszközök biztonságos exportálással való összekapcsolására](platform/itsm-connector-secure-webhook-connections-azure-configuration.md) – új cikk.
- [Az összekötő állapotával kapcsolatos hibák a ITSMC-irányítópulton](platform/itsmc-dashboard-errors.md) – új cikk.
- [Vizsgálja meg a hibákat a ITSMC-irányítópult használatával](platform/itsmc-dashboard.md) – új cikk.
- [Azure metric-riasztások](platform/alerts-troubleshoot-metric.md) – a dinamikus küszöbértékekhez hozzáadott szakaszt tartalmaz.
- [It-szolgáltatásmenedzsmenti csatoló kapcsolatos problémák elhárítása](platform/itsmc-troubleshoot-overview.md) – új cikk.

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights telemetria korreláció](app/correlation.md) – nyomkövetési korreláció hozzáadása, ha egy modul egy másikat hív meg a OpenCensus Pythonban.
- Weblapok [Application Insights](app/javascript.md) – új cikk.
- [Kattintson az Analytics Auto-Collection beépülő modul Application Insights JavaScript SDK](app/javascript-click-analytics-plugin.md) -hoz – új cikk elemre.
- [Alkalmazások figyelése programkód módosítása nélkül – automatikus rendszerállapot-Azure Monitor Application Insights](app/codeless-overview.md) – Python-oszlop hozzáadása.
- [A beépülő modul Application Insights JavaScript SDK](app/javascript-react-plugin.md) -hoz – új cikk.
- [Telemetria processors (előzetes verzió) – a Java-Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md) újraírása.
- [Használat elemzése az Azure Application Insights](app/usage-overview.md) – új cikk.
- [Alkalmazás-változási elemzés használata a Azure monitorban webalkalmazási problémák kereséséhez](app/change-analysis.md) – hozzáadott hiba a üzenetek.


### <a name="insights"></a>Insights    
- [Azure monitor for Azure adatkezelő (előzetes verzió)](insights/data-explorer.md) – új cikk.

### <a name="logs"></a>Naplók    
- [Azure monitor ügyfél által felügyelt kulcs](platform/customer-managed-keys.md) – felhasználóhoz rendelt felügyelt identitás bevezetése.
- A [Azure monitor a dedikált fürtöket naplózza](log-query/logs-dedicated-clusters.md) – frissítette a visszajelzési kódot.
- [Több szolgáltatás lekérdezése – Azure monitor és Azure adatkezelő (előzetes verzió)](platform/azure-monitor-troubleshooting-logs.md) – új cikk.

### <a name="metrics"></a>Mérőszámok
- [Azure monitor metrikák metrikáinak összesítése és megjelenítése](platform/metrics-aggregation-explained.md) – új cikk.

### <a name="platform-logs"></a>Platform naplói
- [Azure monitor erőforrás-naplók támogatott szolgáltatások és kategóriák](platform/resource-logs-categories.md) – új cikk.

### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzetek adatforrásai – az](platform/workbooks-data-sources.md) egyesítés és a változás elemzése is megtörténik.


## <a name="december-2020"></a>2020. december

### <a name="general"></a>Általános
- [Azure monitor ügyfél által felügyelt kulcshoz](platform/customer-managed-keys.md) hozzáadott hibaüzenetek.
- Az Event hub-integrációval Azure Monitor hozzáadott szakasszal [integrált partnerek](platform/partners.md) .

### <a name="agents"></a>Ügynökök
- [Erőforrás-lekérdezés az Azure Adatkezelő Azure monitor](platform/azure-monitor-data-explorer-proxy.md) -új cikk használatával.
- [Az Azure monitoring Agent áttekintése – az](platform/agents-overview.md) Oracle 8 támogatása.

### <a name="alerts"></a>Riasztások
- [Azure metric-riasztások](platform/alerts-troubleshoot-metric.md) – hibaelhárítás a dinamikus küszöbértékekhez.
- [Log Analytics](platform/itsmc-definition.md) – új cikk it-szolgáltatásmenedzsmenti csatoló.
- [It-szolgáltatásmenedzsmenti csatoló áttekintése](platform/itsmc-overview.md) – átstrukturált hibaelhárítási információk.
- [A Cherwell it-szolgáltatásmenedzsmenti csatoló-új cikkel való összekapcsolásához](platform/itsmc-connections-cherwell.md) .
- [A it-szolgáltatásmenedzsmenti csatoló új cikkel való összekapcsoláshoz](platform/itsmc-connections-provance.md) .
- [A SCSM it-szolgáltatásmenedzsmenti csatoló-új cikkel való összekapcsolásához](platform/itsmc-connections-scsm.md) .
- [A ServiceNow it-szolgáltatásmenedzsmenti csatoló-új cikkel való összekapcsolásához](platform/itsmc-connections-servicenow.md) .
- [ServiceNow-szinkronizálási problémák manuális javítása](platform/itsmc-resync-servicenow.md) – átstrukturált hibaelhárítási információk.




### <a name="application-insights"></a>Application Insights
- [Azure Application Insights a JavaScript-webalkalmazásokhoz – a](app/javascript.md) kapcsolati sztring hozzáadása beállítás.
- [Azure Application Insights standard mérőszámok](app/standard-metrics.md) – új cikk.
- [Azure Monitor Application Insights Java](app/java-in-process-agent.md) – további információ az egyéni telemetria az alkalmazásból való elküldéséről.
- [Telemetria folyamatos exportálása Application Insights](app/export-telemetry.md) által hozzáadott diagnosztikai beállításokon alapuló exportálással.
- [A .net-és .net Core-alkalmazások Snapshot Debugger engedélyezése Azure functions](app/snapshot-debugger-function-app.md) – új cikkben.
- Az [Application Insights által használt IP-címek és a log Analytics](app/ip-addresses.md) a Azure Governmenthoz hozzáadott IP-címek.
- [Az Azure Application Insights Profilerval kapcsolatos problémák elhárítása](app/profiler-troubleshooting.md) – további információ a diagnosztikai szolgáltatások helyének bővítmény állapota lapon.
- [Az Azure Application Insights rendelkezésre állási tesztek hibaelhárítása](app/troubleshoot-availability.md) – a ping tesztek hibaelhárításának frissítései.
- [Hibaelhárítás Azure Monitor Application Insights Javához](app/java-standalone-troubleshoot.md) – új cikk.

### <a name="containers"></a>Tárolók
- A [tárolók Azure monitor jelentései](insights/container-insights-reports.md) – új cikk.

### <a name="logs"></a>Naplók
- [Azure monitor naplók dedikált fürtök](log-query/logs-dedicated-clusters.md) – új automatizált parancsok, a leválasztási és eltávolítási módszerek, valamint a hibaelhárítás.
- [Azure monitor és az Azure adatkezelő (előzetes verzió) közötti több szolgáltatás lekérdezése](platform/azure-data-explorer-monitor-cross-service-query.md) – új cikk.
- [Log Analytics munkaterület-adatexportálás Azure monitor (előzetes verzió)](platform/logs-data-export.md) – ARM-sablonok hozzáadása.

### <a name="metrics"></a>Mérőszámok
- [Az Azure Metrikaböngésző speciális funkciói](platform/metrics-charts.md) – további információ az erőforrás-hatókör választóról.
- [Több erőforrás megtekintése Metrikaböngésző](platform/metrics-dynamic-scope.md) – új cikkben.

### <a name="networks"></a>Hálózatok
- [Azure hálózatkezelési elemzési megoldás Azure monitor](insights/azure-networking-analytics.md) – további információ a hálózati elemzések munkafüzetéről.

### <a name="virtual-machines"></a>Virtual Machines
- [Hibrid környezetek Azure monitor engedélyezése](insights/vminsights-enable-hybrid.md) – a függőségi ügynök új verziója.


### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzet-térképi vizualizációk](platform/workbooks-map-visualizations.md) – új cikk.
- [A Azure monitor-munkafüzetek saját tárterületet hozhatnak](platform/workbooks-bring-your-own-storage.md) – új cikk.


## <a name="november-2020"></a>2020. november

### <a name="general"></a>Általános
- [Azure monitor szolgáltatási korlátok](service-limits.md) – frissítve az Azure arc támogatásához.

### <a name="agents"></a>Ügynökök
- [Az Azure monitoring Agent áttekintése](platform/agents-overview.md) – az Azure arc támogatásának frissítése.
- [A Azure monitor-ügynök telepítése](platform/azure-monitor-agent-install.md) – új cikk.
- [Azure monitor-ügynök áttekintése](platform/azure-monitor-agent-overview.md) – az Azure arc támogatásának frissítése.
- [Resource Manager-sablon minták az ügynökökhöz](samples/resource-manager-agent.md) – frissítve az Azure arc támogatásához.

### <a name="alerts"></a>Riasztások
- [Hozzon létre és kezeljen műveleti csoportokat a](platform/action-groups.md) webhookok Azure Portal hozzáadott forrás IP-címeiben.

### <a name="application-insights"></a>Application Insights
- A Java Code unapplication [monitoring Azure monitor Application Insights](app/java-in-process-agent.md) – példa a konfigurációra.
- A [Application Insights JavaScript SDK-hoz tartozó beépülő modult](app/javascript-react-plugin.md) a Reports (reagáló hookok használata) című szakaszban találja.
- [Frissítés Application Insights Java 2. x SDK-ról](app/java-standalone-upgrade-from-2x.md) – új cikk.
- [Kibocsátási megjegyzések a Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](app/snapshot-collector-release-notes.md) – új cikkhez.

### <a name="autoscale"></a>Automatikus méretezés
- [Ismerkedjen meg az Azure](platform/autoscale-get-started.md) -ra épülő, az autoscale egy másik régióba való áthelyezésével foglalkozó témakörben.

### <a name="data-collection"></a>Adatgyűjtés
- [Az adatgyűjtési szolgáltatás konfigurálása a Azure monitor ügynökhöz (előzetes verzió)](platform/data-collection-rule-azure-monitor-agent.md) – az Azure arc támogatásának frissítése.
- [Adatgyűjtési szabályok a Azure monitor (előzetes verzió)](platform/data-collection-rule-overview.md) – frissítve az Azure arc támogatásához.
- [Resource Manager-sablonok az adatgyűjtés szabályaihoz](samples/resource-manager-data-collection-rules.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat
- [Csatlakoztassa az Azure-t a ITSM-eszközökhöz](platform/it-service-management-connector-secure-webhook-connections.md) az ServiceNow-hez való csatlakozáshoz szükséges biztonságos exportálási szakasz használatával.

### <a name="logs"></a>Naplók
- [Log Analytics és Excel](log-query/log-excel.md) – új cikk integrálása.
- [Log Analytics adatbiztonság](platform/data-security.md) – további biztonsági funkciók című szakasz.
- [Log Analytics integráció Power bi](log-query/log-powerbi.md) -új cikkel.
- [Azure monitor rekordok szabványos oszlopai](platform/log-standard-columns.md) – _SubscriptionId oszlop hozzáadva.

Új és frissített cikkek a napló lekérdezési tartalmának átszervezéséről.

- [Log Analytics-oktatóanyag](log-query/log-analytics-tutorial.md)
- [Lekérdezések naplózása Azure Monitor](log-query/log-query-overview.md)
- [A Azure Monitor Log Analytics áttekintése](log-query/log-analytics-overview.md)
- [Példák az Azure Adatkezelő és Azure Monitor lekérdezésére](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Oktatóanyag: Kusto-lekérdezések használata az Azure Adatkezelő és Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

- [Azure monitor for VMS-áttekintés engedélyezése](insights/vminsights-enable-overview.md) – támogatott régiók hozzáadva.

Új cikkek a Azure Monitor for VMs Guest Health szolgáltatáshoz (előzetes verzió)

- [Azure Monitor for VMs Guest Health (előzetes verzió)](insights/vminsights-health-overview.md)
- [Vendég állapottal kapcsolatos riasztások Azure Monitor for VMs (előzetes verzió)](insights/vminsights-health-alerts.md)
- [A figyelés konfigurálása Azure Monitor for VMs vendég állapota (előzetes verzió)](insights/vminsights-health-configure.md)
- [A figyelés konfigurálása Azure Monitor for VMs vendég állapota adatgyűjtési szabályokkal (előzetes verzió)](insights/vminsights-health-configure-dcr.md)
- [Azure Monitor for VMs vendég állapotának engedélyezése (előzetes verzió)](insights/vminsights-health-enable.md)
- [Azure Monitor for VMs Guest Health (előzetes verzió) – problémamegoldás](insights/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>2020. október

### <a name="general"></a>Általános
- [Azure monitor API-nyugdíjazás](platform/operationalinsights-api-retirement.md) – új cikk.

### <a name="agents"></a>Ügynökök
- [Az ügynökök Azure monitor által felügyelt](monitor-reference.md) szakasza.

### <a name="alerts"></a>Riasztások
- [Műveleti csoportokat hozhat létre és kezelhet a](platform/action-groups.md) szolgáltatás címkéje Azure Portal hozzáadott szakaszában.
- [Resource Manager-sablon – minták metrikus riasztásokhoz](samples/resource-manager-alerts-metric.md) – a hozzáadott tartalom egyeztetési paraméter-és tesztelési helyei.
- [Azure metrikus riasztások hibaelhárítása](platform/alerts-troubleshoot-metric.md) – a szabályok konfigurálásának ajánlott gyakorlata.

### <a name="application-insights"></a>Application Insights
- [Szögletes beépülő modul a Application Insights JavaScript SDK](app/javascript-angular-plugin.md) -hoz – új cikk.
- [Azure-Application Insights ASP.net Core alkalmazásokhoz](app/asp-net-core.md) – a ILogger-naplókkal kapcsolatos gyakori kérdések.
- [A ASP.net figyelésének konfigurálása az Azure Application Insightstel](app/asp-net.md) – cikk újraírása.
- [Az Azure Application Insights napló alapú és előre összesített mérőszámai](app/pre-aggregated-metrics-log-metrics.md) – a táblákat előre összevont metrikákkal bővítettük.
- A webhely által hozzáadott [webhelyek rendelkezésre állásának és válaszadásának figyelése](app/monitor-web-app-availability.md) a hely populációs címkéken.
- [Java-alkalmazások figyelése bárhol – Azure Monitor Application Insights](app/java-standalone-config.md) – példa a konfiguráció hozzáadására.
- [Java-alkalmazások figyelése bárhol – Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md) – új cikk.
- [Alkalmazás-módosítási elemzés használata a Azure monitor webalkalmazásokkal kapcsolatos problémák megkereséséhez](app/change-analysis.md) – felvett szakaszt a virtuális gépekhez és a tevékenység naplóhoz.
  
### <a name="autoscale"></a>Automatikus méretezés
- [Ismerkedjen meg az Azure](platform/autoscale-get-started.md) -ra épülő autoskálázással az autoskálázás más régióba való áthelyezésével foglalkozó szakaszban.

### <a name="containers"></a>Tárolók
- [PV-figyelés konfigurálása a Azure monitor for containers](insights/container-insights-persistent-volumes.md) – új cikk.
- [A containers agent Azure monitor kezelése](insights/container-insights-manage-agent.md) – az Azure arc-kompatibilis Kubernetes-fürt támogatása hozzáadva.
- [Metrikai riasztások Azure Monitorről tárolók](insights/container-insights-metric-alerts.md) számára – az Azure arc-kompatibilis Kubernetes-fürt támogatása hozzáadva.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat
- [It-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure monitor](platform/it-service-management-connector-secure-webhook-connections.md) – a ServiceNow-ben hozzáadott szakasz.

### <a name="logs"></a>Naplók
- [Adatok archiválása log Analytics munkaterületről az Azure Storage](platform/logs-export-logic-app.md) -ba a Logic app – új cikk használatával.
- [Log Analytics munkaterület-adatok exportálása Azure monitorban (előzetes verzió)](platform/logs-data-export.md) – az Event hub Rest-kérelméhez hozzáadott minta törzs.
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – a Azure monitor naplók és a Azure Security Center számlázás közötti kapcsolattal kapcsolatos információk. A csomópontok számának és a csomópontok díjszabási szintjeinek használatával hozzáadott lekérdezés. 
- [Log Analytics munkaterület állapotának figyelése Azure monitor](platform/monitor-workspace.md) – új cikk.
- [Adatlekérdezés Azure monitor az Azure adatkezelő (előzetes verzió) használatával](platform/azure-data-explorer-monitor-proxy.md) – új cikk.
- [Azure monitor exportált adatok lekérdezése az Azure adatkezelő (előzetes verzió) használatával](platform/azure-data-explorer-query-storage.md) – új cikk.

### <a name="networks"></a>Hálózatok
- [Hálózatok Azure monitor előzetes](insights/network-insights-overview.md) verzió – hibaelhárítási szakasz. További szakasz a kapcsolathoz.

### <a name="platform-logs"></a>Platformnaplók
- [Azure Activity log esemény sémája](platform/activity-log-schema.md) – a súlyossági szintek leírását adja meg.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
- [Change Analysis in Azure monitor for VMS](insights/vminsights-change-analysis.md) – új cikk.
- [Azure monitor for VMS-áttekintés engedélyezése](insights/vminsights-enable-overview.md) – támogatott régiók hozzáadva.
- A [tárolók Azure monitor frissítése a metrikák](insights/container-insights-update-metrics.md) számára – az Azure arc-kompatibilis Kubernetes-fürt támogatása.



## <a name="september-2020"></a>2020. szeptember

### <a name="general"></a>Általános
- [Azure monitor GYIK](faq.md) – további szakasz a OpenTelemetry.

### <a name="agents"></a>Ügynökök
- [Azure monitor ügynök áttekintése](platform/azure-monitor-agent-overview.md) – az új ügynökre való áttéréshez szükséges döntési tényezők.
- [Az Azure monitoring Agent áttekintése – a](platform/agents-overview.md) Windows 10 támogatása.

### <a name="alerts"></a>Riasztások
- [Log-riasztás létrehozása Azure Resource Manager sablonnal](platform/alerts-log-create-templates.md) – új cikk.
- [Hibaelhárítás az Azure metrikus riasztások](platform/alerts-troubleshoot-metric.md) számára – az ARM-sablon exportálása a metrika riasztási szabályhoz című szakasza.

### <a name="application-insights"></a>Application Insights
- [Hozzon létre egy új Azure Monitor Application Insights munkaterület-alapú erőforrás](app/create-workspace-resource.md) -eltávolított előnézet megjelölését.
- [Adatmegőrzés és tárolás az Azure Application Insightsban](app/data-retention-privacy.md) – további információ a Mac és a Linux adatvesztés elleni védelemhez szükséges új támogatásról.
- [Application Insights – az](app/eventcounters.md) alapértelmezés szerint gyűjtött számlálók megjegyzése.
- [A naplózási és az előre összesített mérőszámok az Azure-ban Application Insights](app/pre-aggregated-metrics-log-metrics.md) – előzetes megjelöléssel eltávolítva.
- [Azure Monitor Application Insights klasszikus erőforrás migrálása munkaterület-alapú erőforrásba](app/convert-classic-resource.md) – új cikk.
- [Java-alkalmazások figyelése bármilyen környezetben – Azure Monitor Application Insights](app/java-in-process-agent.md) – az ügynök új előzetes verziójának frissítése.
- [Webalkalmazás-elemzések beállítása a ASP.net-hez az Azure Application Insightsban](app/asp-net.md) – cikk újraírása.
- [Telemetria csatornák Az Azure Application Insightsban](app/telemetry-channels.md) – további információ a Mac és a Linux adatvesztés elleni védelemhez szükséges új támogatásról.
- Az [Azure Application Insights Snapshot Debugger](app/snapshot-debugger-troubleshoot.md) – SSL-című szakasz hibaelhárítása Snapshot Debugger.
- [Alkalmazás-változási elemzés használata a Azure monitorban webalkalmazási problémák kereséséhez](app/change-analysis.md) – a virtuális gép és a tevékenység naplójának hozzáadása.


### <a name="containers"></a>Tárolók
- Az [Azure arc-kompatibilis Kubernetes-fürt konfigurálása az Azure monitor for containers](insights/container-insights-enable-arc-enabled-clusters.md) szolgáltatással – további útmutatást nyújt az egyszerű szolgáltatásnév használatával történő figyelés engedélyezéséhez.
- [Üzembe helyezés & hPa-metrikák Azure monitor for containers](insights/container-insights-deployment-hpa-metrics.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat
- [Az Azure cache](insights/redis-cache-insights-overview.md) -hez készült Azure monitor Redis – eltávolított előnézet megjelölése.
- [Azure monitor hálózatok számára (előzetes verzió) – a](insights/network-insights-overview.md) kapcsolat és a forgalom további részei.
- [It-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure monitor](platform/it-service-management-connector-secure-webhook-connections.md) – új cikk.
- [IT-szolgáltatásmenedzsmenti csatoló Azure monitor](platform/itsmc-connections.md) -Megjegyzés a Cherwell és az ITSM-integrációk terén.
- [Key Vault figyelése Azure Monitor Key Vault](insights/key-vault-insights-overview.md) -eltávolított előnézet megjelöléssel.

### <a name="logs"></a>Naplók
- [Lekérdezések naplózása Azure monitor log lekérdezésekben](log-query/query-audit.md) – új cikk.
- [Azure monitor ügyfél által felügyelt kulcshoz](platform/customer-managed-keys.md) hozzáadott ügyfél-kulcstároló.
- [Azure monitor naplók dedikált fürtök](log-query/logs-dedicated-clusters.md) – új cikk.
- [A Azure monitor naplók üzembe helyezésének megtervezése – a](platform/design-logs-deployment.md) méretezési és a betöltési mennyiségre vonatkozó korlátozás szakasz frissítése.
- [A lekérdezés hatókörének naplózása Azure Monitor log Analytics](log-query/scope.md) -a alkalmazásban a munkaterület-alapú alkalmazások belefoglalása.
- [Naplók Azure monitor-a](platform/data-platform-logs.md) munkaterület-alapú alkalmazásokat tartalmazó frissítések.
- [A Azure monitor rekordok szabványos oszlopai – a](platform/log-standard-columns.md) frissítések, amelyek munkaterületen alapuló alkalmazásokat tartalmaznak.
- [Azure monitor szolgáltatás korlátai](service-limits.md) – a felhasználói lekérdezések szabályozásának frissített korlátai.
- [Az ügyfél által felügyelt Storage-fiókok használata Azure Monitor log Analyticsban – a](platform/private-storage.md) cikk átírása megtörténik.
- Az [Azure log Analytics-ben tárolt adatok megtekintése és elemzése](./platform/data-platform-logs.md) – a frissítések a munkaterület-alapú alkalmazások belefoglalásához szükségesek.


### <a name="platform-logs"></a>Platformnaplók
- [Azure Activity log esemény sémája – Azure monitor](platform/activity-log-schema.md) – súlyossági szintek hozzáadása.
- [Resource Manager-sablonok – minták a diagnosztikai beállításokhoz](samples/resource-manager-diagnostic-settings.md) – az Azure Storage-fiókhoz hozzáadott minta.

### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzet-diagram vizualizációi](platform/workbooks-chart-visualizations.md) – új cikk.
- [Azure monitor munkafüzet összetett sávjának renderelése](platform/workbooks-composite-bar.md) – új cikk.
- [Azure monitor munkafüzet Graph vizualizációk](platform/workbooks-graph-visualizations.md) – új cikk.
- [Azure monitor munkafüzet Grid vizualizációk](platform/workbooks-grid-visualizations.md) – új cikk.
- [Azure monitor munkafüzet mézes fésű vizualizációk](platform/workbooks-honey-comb.md) – új cikk.
- [Azure monitor munkafüzet szövegének vizualizációi](platform/workbooks-text-visualizations.md) – új cikk.
- [Azure monitor munkafüzet csempe vizualizációk](platform/workbooks-tile-visualizations.md) – új cikk.
- [Azure monitor munkafüzet favizualizációi](platform/workbooks-tree-visualizations.md) – új cikk.




## <a name="august-2020"></a>2020. augusztus

### <a name="general"></a>Általános

- A [Azure monitor által figyelt](monitor-reference.md) frissítések Azure monitor ügynök belefoglalásával.


### <a name="agents"></a>Ügynökök
- [Azure monitor ügynök áttekintése](platform/azure-monitor-agent-overview.md) – új cikk.
- [Azure monitor engedélyezése hibrid környezethez](insights/vminsights-enable-hybrid.md) – frissített függőségi ügynök verziója.
- [Az Azure monitoring-ügynökök áttekintése](platform/agents-overview.md) – Azure monitor ügynök és összevont operációs rendszer támogatási táblázata.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Új és frissített cikkek az ügynök tartalmának újrastrukturálása esetén
- [Azure Monitor for VMs-áttekintés engedélyezése](insights/vminsights-enable-overview.md)
- [Log Analytics-ügynök telepítése Linux rendszerű számítógépeken](platform/agent-linux.md)
- [Log Analytics-ügynök telepítése Windows rendszerű számítógépeken](platform/agent-windows.md)
- [Log Analytics-ügynök áttekintése](platform/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights a JavaScript-webalkalmazásokhoz](app/javascript.md) – hozzáadott szakasz az ügyfél-kiszolgáló korrelációjának és konfigurációjának pontosítása a CORS korreláció érdekében.
- [Hozzon létre egy új Azure Monitor Application Insights munkaterület-alapú erőforrások](app/create-workspace-resource.md) által biztosított képességek a munkaterület-alapú alkalmazások számára.
- A [Application Insights és log Analytics által használt IP-címek](app/ip-addresses.md) az élő metrikák adatfolyamának frissített IP-címei.
- [Java-alkalmazások figyelése bármilyen környezetben – Azure Monitor Application Insights](app/java-in-process-agent.md) – a támogatott egyéni telemetria tartozó táblázat hozzáadva.
- [Natív reagáló beépülő modul a Application Insights JavaScript SDK](app/javascript-react-native-plugin.md) -hoz – új cikk.
- [A beépülő modul Application Insights JavaScript SDK](app/javascript-react-plugin.md) -hoz – új cikk.
- [Resource Manager-sablon minta Azure Function-alkalmazások létrehozásához Application Insights monitoring](samples/resource-manager-function-app.md) – új cikk.
- [Resource Manager-sablonok az Azure app Services Web Apps és a Application Insights monitoring](samples/resource-manager-web-app.md) – új cikk létrehozásához.
- [Használat elemzése az Azure Application Insights](app/usage-overview.md) – videó hozzáadva.

### <a name="autoscale"></a>Automatikus méretezés
- [Ismerkedjen meg az Azure-beli autoskálázással](platform/autoscale-get-started.md) , amely a app Service kifogástalan állapotú példányain való útválasztást ismerteti.

### <a name="data-collection"></a>Adatgyűjtés
- [Az adatgyűjtési szolgáltatás konfigurálása a Azure monitor-ügynökhöz (előzetes verzió)](platform/data-collection-rule-azure-monitor-agent.md) – új cikk.
- Adatgyűjtés [szabályai a Azure monitor (előzetes verzió)](platform/data-collection-rule-overview.md) – új cikk.


### <a name="containers"></a>Tárolók
- [Üzembe helyezés & hPa-metrikák Azure monitor for containers](insights/container-insights-deployment-hpa-metrics.md) – új cikk.

### <a name="insights"></a>Insights
- [Azure monitor figyelési megoldásai](insights/solutions.md) – új felhasználói felület frissítése.
- [Network Performance monitor megoldás az Azure-ban](insights/network-performance-monitor.md) – támogatott munkaterület-régiók.


### <a name="logs"></a>Naplók
- [Azure monitor GYIK](faq.md) – az adatok munkaterületről való törléséhez hozzáadott bejegyzés. Bejegyzés hozzáadva a 502-es és a 503-es válaszokhoz.
  - [A Azure monitor naplók üzembe helyezésének megtervezése – a](platform/design-logs-deployment.md) betöltési mennyiségre vonatkozó frissítési korlát szakasz.
- [Azure monitor naplók használatának és költségeinek kezelése – a](platform/manage-cost-storage.md) frissített használati lekérdezések hatékonyabbá teszi a lekérdezési formátumot.
- [A Azure monitor](log-query/query-optimization.md) -specifikus értékek a teljesítménymutatók számára történő optimalizálása.
- [Resource Manager-sablonok – minták a diagnosztikai beállításokhoz](samples/resource-manager-diagnostic-settings.md) – a napló lekérdezési naplóihoz hozzáadott minta.


### <a name="platform-logs"></a>Platformnaplók
- [Diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre való küldéséhez](platform/diagnostic-settings.md) – a diagnosztikai beállításokhoz hozzáadott regionális követelmény.

### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzetek áttekintése](platform/workbooks-overview.md) – videó hozzáadva.
- [Azure-munkafüzet sablonjának áthelyezése egy másik régióba](platform/workbook-templates-move-region.md) – új cikk.
- [Azure-munkafüzetek áthelyezése egy másik régióba](platform/workbooks-move-region.md) – új cikk.



## <a name="july-2020"></a>2020. július

### <a name="general"></a>Általános
- [Azure monitor üzembe helyezése](deploy-scale.md) Azure monitor for VMS bevezetési tartalom újrastrukturálása.
- Az [Azure Private link használatával biztonságosan csatlakoztathatja a hálózatokat Azure monitor](platform/private-link-security.md) hozzáadott szakaszhoz a korlátok között.

### <a name="alerts"></a>Riasztások
- [Azure monitor riasztásokra vonatkozó műveleti szabályok](platform/alerts-action-rules.md) – CLI-folyamatok hozzáadva.
- [Műveleti csoportok létrehozása és kezelése a Azure Portalban](platform/action-groups.md) – frissítve a felhasználói felület változásainak megfelelően.
- [Példa lekérdezésekre Azure Monitor log Analytics](log-query/example-queries.md) – új cikk.
- A riasztási szabály kvótájának Azure Monitor által hozzáadott szakasza [riasztások hibakeresése](platform/alerts-troubleshoot-log.md) .
- Az [Azure metrika riasztásai](platform/alerts-troubleshoot-metric.md) – a riasztási szabály hozzáadott szakasza egy olyan egyéni metrika esetében, amely még nincs kibocsátva.
- [Ismerje meg, hogyan működnek a metrikus riasztások Azure Monitorban.](platform/alerts-metric-overview.md) -Javaslat hozzáadva az összesítési részletesség kiválasztásához.

### <a name="application-insights"></a>Application Insights
- [Az Azure Web App Extension kibocsátási megjegyzései – Application Insights](app/web-app-extension-release-notes.md) – új cikk.
- [Resource Manager-sablonok – minták Application Insights erőforrásokhoz](samples/resource-manager-app-resource.md) – új cikk.
- Az [Azure Application Insights Profiler-vel kapcsolatos problémák elhárítása](app/profiler-troubleshooting.md) – megjegyzés a ASP.net Core-alkalmazások Azure app Service-on futó hiba a Profilerben. 

### <a name="containers"></a>Tárolók
- A [Azure monitor for containers – új cikk értesítéseinek naplózása](insights/container-insights-log-alerts.md) .
- [Metrikai riasztások Azure monitor a tárolók számára](insights/container-insights-metric-alerts.md) – új cikk.

### <a name="logs"></a>Naplók
- [Azure monitor ügyfél által felügyelt kulcshoz](platform/customer-managed-keys.md) hozzáadott hibaüzenet és szakasz CMK-konfiguráció lekérdezésekhez.
- [Azure monitor http](platform/data-collector-api.md) -adatgyűjtő API – Python 3 minta hozzáadva.
- [Az](log-query/query-optimization.md) allekérdezések használatakor a Azure monitor hozzáadott szakasz több adatvizsgálat elkerülését ismertető szakaszban található.
- [Oktatóanyag: Ismerkedés a log Analytics lekérdezésekkel](./log-query/log-analytics-tutorial.md) – videó hozzáadva.

### <a name="platform-logs"></a>Platformnaplók
- [Diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre való küldéséhez](platform/diagnostic-settings.md) – hozzáadott videó.
- [Resource Manager-sablon minták a Azure monitor](samples/resource-manager-samples.md) hozzáadott ARM-minta a naplók célhelyének típusával. 

### <a name="solutions"></a>Megoldások
- [Figyelési megoldások Azure monitor](insights/solutions.md) – CLI-folyamatok.
- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – megváltozott a nyugdíjazás dátuma.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

Új és frissített cikkek Azure Monitor for VMs tartalom átszervezéséről

- [Mi az Azure Monitor for VMs?](insights/vminsights-overview.md)
- [Azure Monitor for VMs Log Analytics munkaterület konfigurálása](insights/vminsights-configure-workspace.md)
- [Linux rendszerű számítógépek összekapcsolásának Azure Monitor](platform/agent-linux.md)
- [Hibrid környezetek Azure Monitorának engedélyezése](insights/vminsights-enable-hybrid.md)
- [Azure Monitor engedélyezése egyetlen virtuális gép vagy virtuálisgép-méretezési csoport számára a Azure Portal](insights/vminsights-enable-portal.md)
- [Azure Monitor for VMs engedélyezése Azure Policy használatával](./insights/vminsights-enable-policy.md)
- [Azure Monitor for VMs-áttekintés engedélyezése](insights/vminsights-enable-overview.md)
- [Azure Monitor for VMs engedélyezése a PowerShell használatával](insights/vminsights-enable-powershell.md)
- [Azure Monitor for VMs engedélyezése Resource Manager-sablonok használatával](insights/vminsights-enable-resource-manager.md)
- [Azure Monitor for VMs engedélyezése a PowerShell vagy a sablonok használatával](./insights/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Vizualizációk
- [Log Analytics irányítópult-vizualizációk frissítése](log-query/dashboard-upgrade.md) – frissített frissítési sebesség.
- [Adatok megjelenítése Azure monitor](visualizations.md) által hozzáadott videóból.


## <a name="june-2020"></a>2020. június

### <a name="general"></a>Általános
- [Azure monitor üzembe helyezése](deploy-scale.md) – új cikk.
- [Azure monitor ügyfél által felügyelt kulcs](platform/customer-managed-keys.md) – frissített billingtype tulajdonság. PowerShell-parancsok hozzáadva.

### <a name="agents"></a>Ügynökök
- [Log Analytics Agent áttekintése](platform/log-analytics-agent.md) – a Python 2 követelményének hozzáadása.

### <a name="alerts"></a>Riasztások
- [Riasztási szabályok vagy műveleti szabályok frissítése, ha a célként megadott erőforrás egy másik Azure-régióba kerül át](platform/alerts-resource-move.md) – új cikk.
- [Azure metric-riasztások hibaelhárítása](platform/alerts-troubleshoot-metric.md) – új cikk.
- [Riasztások hibaelhárítása Azure monitor](platform/alerts-troubleshoot-metric.md) – új cikk.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights JavaScript-webalkalmazásokhoz](app/javascript.md) – frissítés a JavaScript SDK-hoz szakasz. Frissített kódrészlet a betöltési hibák jelentéséhez.
- [A BYOS konfigurálása (saját tárhely használata) a Profiler & Snapshot Debugger](app/profiler-bring-your-own-storage.md) – új cikk.
- [Bejövő kérelmek nyomon követése az Azure Application Insights a OpenCensus Pythonban](app/opencensus-python-request.md) – frissített naplózás és konfiguráció a OpenCensus.
- [Élő ASP.net-webalkalmazás figyelése az Azure Application Insights](app/monitor-performance-live-website-now.md) – a Állapotmonitor v1 frissítésének elavult dátuma.
- [Node.js szolgáltatások figyelése az Azure Application Insights](app/nodejs.md) – több frissítés, beleértve a áteresztő-verziók és az SDK-konfiguráció áttelepítését
- [Python-alkalmazások figyelése Azure monitor (előzetes verzió)](app/opencensus-python.md) – további szakasz a Azure monitor-exportőrök konfigurálásához.
- [Alkalmazások figyelése programkód módosítása nélkül – automatikus rendszerállapot-Azure Monitor Application Insights](app/codeless-overview.md) – új cikk.
- [Az SDK-Betöltési hiba elhárítása JavaScript-webalkalmazások esetén](app/javascript-sdk-load-failure.md) – új cikk.

### <a name="containers"></a>Tárolók
- [A hibrid Kubernetes-fürthöz tartozó új szakasz figyelésének leállítása](insights/container-insights-optout-hybrid.md) az ív-kompatibilis Kubernetes.
- [Azure arc-kompatibilis Kubernetes-fürt konfigurálása Azure monitor for containers](insights/container-insights-enable-arc-enabled-clusters.md) – új cikk.
- Az [Azure Red Hat OpenShift v4. x konfigurálása Azure monitor for containers esetében](insights/container-insights-azure-redhat4-setup.md) – frissített előfeltételek.
- A [tárolók élő adatainak (előzetes verzió) Azure monitor beállítása](insights/container-insights-livedata-setup.md) – eltávolított Megjegyzés az Azure US government szolgáltatásban nem elérhető funkciókról.

### <a name="insights"></a>Insights
- [Gyakori kérdések – Network Performance monitor megoldás az Azure-ban](insights/network-performance-monitor-faq.md) – ExpressRoute-figyelő – gyakori kérdések.

### <a name="logs"></a>Naplók
- [Az Azure log Analytics-munkaterület törlése és helyreállítása](platform/delete-workspace.md) – PowerShell-parancs hozzáadva. Frissített hibaelhárítás.
- [Log Analytics munkaterületek kezelése Azure monitor](platform/manage-access.md) – példa a nem engedélyezett táblákra az Azure RBAC szakaszban.
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – az adatméret számításával kapcsolatos további részletek. Frissült az adatmennyiség-riasztások konfigurálása. Az Azure Sentinel által összegyűjtött biztonsági adatok részletei. Az adatsapka pontosítása.
- [Azure monitor naplók használata a Azure Logic apps és a Power automatizáló](platform/logicapp-flow-connector.md) által hozzáadott összekötő-korlátokkal.

### <a name="metrics"></a>Mérőszámok
- [Azure monitor támogatott mérőszámok erőforrástípus szerint](platform/metrics-supported.md) – frissített SQL Server metrikák.


### <a name="platform-logs"></a>Platformnaplók

- [Resource Manager-sablonok – minták a diagnosztikai beállításokhoz](samples/resource-manager-diagnostic-settings.md) – javítás a műveletnapló diagnosztikai beállításához.
- [Azure-tevékenység naplójának küldése log Analytics munkaterületre Azure Portal](learn/quick-collect-activity-log-portal.md) – új cikk használatával.
- [Azure-tevékenység naplójának küldése log Analytics munkaterületre Azure Resource Manager sablon használatával](learn/quick-collect-activity-log-arm.md) – új cikk.

Új és frissített cikkek a platform log-tartalmak újrastrukturálása és összevonása terén

- [Azure-beli erőforrás-naplók archiválása a Storage-fiókba](./platform/resource-logs.md#send-to-azure-storage)
- [Azure Activity log esemény sémája](platform/activity-log-schema.md)
- [Azure-tevékenység naplója](platform/activity-log.md)
- [CLI-minták Azure Monitor](samples/cli-samples.md)
- [PowerShell-minták Azure Monitor](samples/powershell-samples.md)
- [Útmutató az Azure Monitor REST API-hoz](platform/rest-api-walkthrough.md)
- [Az Azure Resource naplózza a támogatott szolgáltatásokat és sémákat](./platform/resource-logs-schema.md)
- [Azure-erőforrásnaplók](platform/resource-logs.md)
- [Azure-beli tevékenységek naplójának összegyűjtése és elemzése Azure Monitor](./platform/activity-log.md)
- [Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Diagnosztikai beállítások létrehozása a platformnaplók és -metrikák más célhelyekre való küldéséhez](platform/diagnostic-settings.md)
- [Az Azure-tevékenység naplójának exportálása](./platform/activity-log.md#legacy-collection-methods)
- [Az Azure-platform naplóinak áttekintése](platform/platform-logs-overview.md)
- [Azure platform-naplók továbbítása egy Event hubhoz](./platform/resource-logs.md#send-to-azure-event-hubs)
- [Azure-Tevékenységnaplók eseményeinek megtekintése Azure Monitor](./platform/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
- [Engedélyezze a Azure Portal](./insights/vminsights-enable-portal.md) -frissítve Azure monitor for VMS az Azure arc befoglalását.
- Az Azure arc befoglalásának [engedélyezése Azure monitor for VMS áttekintése](insights/vminsights-enable-overview.md) – frissítve.
- [Mi az Azure Monitor for VMs?](insights/vminsights-overview.md) -Frissítve az Azure arc befoglalásával.


### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzetek adatforrásai](platform/workbooks-data-sources.md) – a riasztások és az egyéni végpontok szakasz.
- [Hibaelhárítás Azure monitor munkafüzet-alapú bepillantást](insights/troubleshoot-workbooks.md) – új cikk.
- [Log Analytics irányítópult-vizualizációk frissítése](log-query/dashboard-upgrade.md) – új cikk.



## <a name="may-2020"></a>2020. május

### <a name="general"></a>Általános

- [Azure monitor GYIK](faq.md) – további szakasz a mérőszámokhoz.
- [Azure monitor ügyfél által felügyelt kulcs](platform/customer-managed-keys.md) – az általános elérhetőségre való felkészülés különböző változásai.
- [Beépített szabályzat-definíciók a Azure monitor](./samples/policy-reference.md) új cikkhez.
- [Felhasználói tulajdonban lévő Storage-fiókok a naplók](platform/private-storage.md) betöltéséhez – új cikk.
- [A Azure monitor-naplók használatának és költségeinek kezelése – a](platform/manage-cost-storage.md) fürt arányos számlázással bővült.
- Az [Azure Private link használatával biztonságosan csatlakoztathatja a hálózatokat Azure monitor](platform/private-link-security.md) új cikkhez.


#### <a name="new-resource-manager-template-samples"></a>Új Resource Manager-sablonok – minták 
- [Resource Manager-sablonok – minták a Azure Monitor](samples/resource-manager-samples.md)
- [Resource Manager-sablonok – minták a műveleti csoportokhoz](samples/resource-manager-action-groups.md)
- [Resource Manager-sablonok – minták az ügynökökhöz](samples/resource-manager-agent.md)
- [Resource Manager-sablonok minták a tárolók Azure Monitoréhez](samples/resource-manager-container-insights.md)
- [Resource Manager-sablonok – minták a Azure Monitor for VMs](samples/resource-manager-vminsights.md)
- [Resource Manager-sablonok – minták a diagnosztikai beállításokhoz](samples/resource-manager-diagnostic-settings.md)
- [Resource Manager-sablonok – minták Log Analytics munkaterületekhez](samples/resource-manager-workspace.md)
- [Resource Manager-sablonok – minták a naplók lekérdezéséhez](samples/resource-manager-log-queries.md)
- [Resource Manager-sablonok – minták a naplózási lekérdezés riasztási szabályaihoz](samples/resource-manager-alerts-log.md)
- [Resource Manager-sablonok – minták metrikus riasztási szabályokhoz](samples/resource-manager-alerts-metric.md)
- [Resource Manager-sablonok – minták a munkafüzetek számára](samples/resource-manager-workbooks.md)

### <a name="agents"></a>Ügynökök
- [Telepítse és konfigurálja a Windows Azure Diagnostics bővítményt (wad)](platform/diagnostics-extension-windows-install.md) – további részletek a diagnosztika konfigurálásáról.
- [Log Analytics Agent áttekintése](platform/log-analytics-agent.md) – támogatott Linux-verziók hozzáadása.

### <a name="application-insights"></a>Application Insights

- [Azure functions futó alkalmazások figyelése Application Insights-Azure monitor](app/monitor-functions.md) -új cikkben.
- [Node.js szolgáltatások figyelése az Azure Application Insights](app/nodejs.md) -általános frissítésekkel, beleértve az előző verziókról való áttelepítés új szakaszát.
- Az [Application Insights által használt IP-címek](app/ip-addresses.md) és a log Analytics – a webhookok és az USA kormányzati szervei által hozzáadott IP-címek.
- [Alkalmazások figyelése az Azure Kubernetes szolgáltatásban (ak) Application Insights-Azure monitor](app/kubernetes-codeless.md) -új cikkel.
- A .NET által hozzáadott [adatokkal kapcsolatos hibák elhárítása](app/asp-net-troubleshoot-no-data.md) – a naplók a DotNet-tracevel való összegyűjtésével kapcsolatos Application Insights.
- [Alkalmazás-változási elemzés használata a Azure monitorban webes alkalmazások problémáinak kereséséhez](app/change-analysis.md) – több frissítés a Change Analysis funkcióban.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Új és frissített cikkek a munkaterület-alapú alkalmazások előzetes verziójához
- [Azure Monitor Application Insights munkaterület-alapú erőforrás-sémája](app/apm-tables.md)
- [Új Azure Monitor Application Insights munkaterület-alapú erőforrás létrehozása](app/create-workspace-resource.md)
- [alkalmazás () kifejezés Azure Monitor log lekérdezésekben](log-query/app-expression.md)
- [A lekérdezés hatókörének naplózása Azure Monitor Log Analytics](log-query/scope.md)
- [Erőforrások lekérdezése Azure Monitor](log-query/cross-workspace-query.md)
- [Azure Monitor naplóbejegyzések szabványos tulajdonságai](./platform/log-standard-columns.md)
- [Azure Monitor naplók szerkezete](./platform/data-platform-logs.md)





### <a name="containers"></a>Tárolók
- A [tárolók Azure monitorának engedélyezése](insights/container-insights-onboard.md) – frissített tűzfal-konfigurációs tábla.
- A [tárolók Azure monitorának frissítése a metrikák](insights/container-insights-update-metrics.md) számára – frissítés a felügyelt identitások használatához a metrikák összegyűjtéséhez.
- [A tárolók Azure monitor figyelési díja](insights/container-insights-cost.md) – új cikk.
- [Azure monitor beállítása a tárolók élő adatához (előzetes verzió)](insights/container-insights-livedata-setup.md) – az új fürt szerepkör-kötés támogatása.

### <a name="insights"></a>Insights
- [Azure monitor az Azure cache for Redis (előzetes verzió)](insights/redis-cache-insights-overview.md) – új cikk.
- [Key Vault figyelése Azure monitor for Key Vault (előzetes verzió)](./insights/key-vault-insights-overview.md) – új cikk.

### <a name="logs"></a>Naplók
- [Hozzon létre & konfigurálja a log Analyticst a PowerShell](platform/powershell-workspace-configuration.md) által hozzáadott hibaelhárítási szakaszban.
- [Hozzon létre egy log Analytics munkaterületet a Azure Portal](learn/quick-create-workspace.md) által felvett hibaelhárítás szakaszban.
- [Log Analytics munkaterület létrehozása az Azure CLI](learn/quick-create-workspace-cli.md) -vel – új hibaelhárítási szakasz.
- Az [Azure log Analytics munkaterület törlése és helyreállítása](platform/delete-workspace.md) – frissített információk a törölt munkaterület helyreállításáról.
- [Függvények Azure monitor log lekérdezésekben](log-query/functions.md) – eltávolított Megjegyzés a függvények nem tartalmazó függvényekről.
- [Azure monitor naplók szerkezete – a](./platform/data-platform-logs.md) Application Insights táblázatra vonatkozó egyértelmű tulajdonságok leírása.
- [Használjon Azure monitor naplókat a Azure Logic apps és a Power Automate](platform/logicapp-flow-connector.md) – hozzáadási korlátok szakaszban.
- [A PowerShell használatával hozzon létre és konfiguráljon egy log Analytics munkaterület](platform/powershell-workspace-configuration.md) által hozzáadott hibaelhárítás szakaszt.


### <a name="metrics"></a>Mérőszámok
- [Azure monitor támogatott mérőszámok erőforrástípus szerint](platform/metrics-supported.md) – egyértelmű vendég metrikák és metrikák útválasztása. 

### <a name="solutions"></a>Megoldások
- [Optimalizálja Active Directory-környezetét Azure monitor](insights/ad-assessment.md) által támogatott verziókhoz hozzáadott Windows Server 2019-es verzióval.
- [Optimalizálja SQL Server-környezetét a Azure monitor](insights/sql-assessment.md) által támogatott SQL Server-verziókhoz.


### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
- A [Azure monitor for VMS áttekintésének engedélyezése](insights/vminsights-enable-overview.md) – az Ubuntu Server támogatott verzióihoz lett hozzáadva. Log Analytics munkaterület által támogatott régiók lettek hozzáadva.
- A [teljesítmény diagramon való Azure monitor for VMS](insights/vminsights-performance.md) hozzáadásával kapcsolatos korlátozások szakasz a nem elérhető metrikák esetében.

### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzetek és Azure Resource Manager sablonok](platform/workbooks-automate.md) – a Resource Manager-frissítés hozzáadva egy munkafüzet-sablon telepítéséhez.
- [Azure monitor munkafüzetek csoportjai](platform/workbooks-groups.md) – új cikk.
- [Azure monitor munkafüzetek – JSON-adatátalakítás JSONPath](platform/workbooks-jsonpath.md) – új cikk.


## <a name="april-2020"></a>2020. április

### <a name="general"></a>Általános

- A [Azure monitor ügyfél által felügyelt kulcshoz](platform/customer-managed-keys.md) hozzáadott szakasz az aszinkron műveletekhez
- [Log Analytics munkaterületek kezelése Azure monitor](platform/manage-access.md) – frissített egyéni naplók szakaszban.

### <a name="alerts"></a>Riasztások

- [Azure monitor riasztásokra vonatkozó műveleti szabályok](platform/alerts-action-rules.md) – videó hozzáadva.
- [A riasztások és az értesítések monitorozásának áttekintése az Azure-ban](platform/alerts-overview.md) – videó hozzáadva.

### <a name="application-insights"></a>Application Insights

- [Az Azure Application Insights-ban elérhető alkalmazás-hozzárendelés](app/app-map.md) – a Java-ügynökhöz hozzáadott Felhőbeli szerepkörök nevei.
- [Azure Application Insights .net-ügynök API](app/status-monitor-v2-api-reference.md) -referenciája – összevont API-hivatkozás.
- Az [Application Insights és log Analytics által használt IP-címek](app/ip-addresses.md) az alkalmazás-elemzések és az log Analytics API-k, a műveleti csoport webhookok, az Azure USA kormánya számára.
- [Java-alkalmazások figyelése bárhol](app/java-standalone-config.md) – új cikk.
- [Java-alkalmazások figyelése bármilyen környezetben](app/java-in-process-agent.md) – új cikk.
- [Bármilyen környezetben futó Java-alkalmazások figyelése](app/java-standalone-arguments.md) – új cikk.
- [Helyszínen futó Java-alkalmazások figyelése](app/java-on-premises.md) – új cikk.
- [Application Insights eltávolítása a Visual Studióban](app/remove-application-insights.md) – új cikk.
- [Telemetria-mintavételezés az Azure Application Insightsban](app/sampling.md) – javítás a Pythonban rögzített sebességű mintában.

### <a name="containers"></a>Tárolók

- Az [Azure Red Hat OpenShift v4. x konfigurálása Azure monitor for containers szolgáltatással](insights/container-insights-azure-redhat4-setup.md) – új cikk.
- [ServiceNow-szinkronizálási problémák manuális javítása](platform/itsmc-resync-servicenow.md) – új cikk.
- [Az Azure és a Red Hat OpenShift v4-fürt figyelésének leállítása](insights/container-insights-optout-openshift-v4.md) – új cikk.
- [Az Azure Red Hat OpenShift v3-fürt figyelésének leállítása](insights/container-insights-optout-openshift-v3.md) – új cikk.
- [A hibrid Kubernetes-fürt figyelésének leállítása](insights/container-insights-optout-hybrid.md) – új cikk.

### <a name="insights"></a>Insights

- [Azure Key vaultok figyelése Azure monitor a Key vaultok számára (előzetes verzió)](insights/key-vault-insights-overview.md) – új cikk.

### <a name="logs"></a>Naplók

- [Azure monitor szolgáltatás korlátai](service-limits.md) – a felhasználói lekérdezések szabályozása.
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – a naplókhoz hozzáadott számlázás. Kusto-lekérdezés hozzáadva, amely lehetővé teszi az ügyfelek számára a csomópontok közötti örökölt díjszabási szintet annak megállapítására, hogy a felhasználóknak GB vagy Capacity foglalási szinten kell áttérniük.

### <a name="metrics"></a>Mérőszámok

- [Az Azure Metrikaböngésző](platform/metrics-charts.md) által hozzáadott összesítési szakasz speciális funkciói.

### <a name="workbooks"></a>Munkafüzetek

- [Azure monitor munkafüzetek és Azure Resource Manager sablonok](platform/workbooks-automate.md) – a munkafüzet-sablonok üzembe helyezéséhez hozzáadott Resource Manager-sablon.

## <a name="march-2020"></a>2020. március

### <a name="general"></a>Általános

- [Azure monitor áttekintése](overview.md) – Azure monitor áttekintő videó hozzáadva.
- [Azure monitor ügyfél által felügyelt kulcs konfigurációjának](platform/customer-managed-keys.md) általános frissítései.
- [Azure monitor adathivatkozás](/azure/azure-monitor/reference/) – új webhely.

### <a name="alerts"></a>Riasztások

- [Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure monitor – a](platform/alerts-activity-log.md) Resource Manager-sablonok további magyarázata.
- [Ismerje meg, hogyan működnek a metrikus riasztások Azure Monitorban.](platform/alerts-metric-overview.md) -Frissítve a kormányzati támogatáshoz.
- [Hibaelhárítás Azure monitor riasztások és értesítések](platform/alerts-troubleshoot.md) – új cikk.

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights automatizálása a PowerShell](app/powershell.md) által hozzáadott ARMClient-példákkal.
- [Telemetria folyamatos exportálása a Application Insightsból](app/export-telemetry.md) – tábla hozzáadása az exportálási struktúra részleteivel.
- [Snapshot Debugger engedélyezése a .NET-alkalmazásokhoz Azure app Service](app/snapshot-debugger-appservice.md) – hozzáadott Resource Manager-sablon – példa.
- [Az Azure Application Insights-használat és-költségek kezelése](app/pricing.md) az adatkorláttal kapcsolatos riasztásokkal kapcsolatban.
- [Python-alkalmazások figyelése Azure monitor (előzetes verzió)](app/opencensus-python.md) – szabványos metrikák hozzáadása.
- [Forrás-hozzárendelési támogatás JavaScript-alkalmazásokhoz – Azure Monitor Application Insights](app/source-map-support.md) – új cikk.

### <a name="containers"></a>Tárolók

- [Azure monitor GYIK](faq.md) – Azure monitor tárolók frissítése.
- [GPU-figyelés konfigurálása a Azure monitor for containers](insights/container-insights-gpu-monitoring.md) – új cikk.

### <a name="insights"></a>Insights

- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – frissített elavulás dátuma.

### <a name="logs"></a>Naplók

- A Azure Monitor által hozzáadott CPU-feltételhez tartozó [napló-lekérdezések optimalizálása](log-query/query-optimization.md) az XML-és JSON-elemzésekhez.
- [Az Azure log Analytics munkaterület törlése és helyreállítása](platform/delete-workspace.md) – hibaelhárítás.
- [Azure monitor naplók használata Azure Logic apps és energiagazdálkodási automatizálással](platform/logicapp-flow-connector.md) – frissítve az új Azure monitor-összekötőhöz.

### <a name="metrics"></a>Mérőszámok

- [A lemez metrikáinak elavulása a Azure Portal](platform/portal-disk-metrics-deprecation.md) új cikkben.
- [Oktatóanyag – mérőszámok diagramjának létrehozása Azure monitor](learn/tutorial-metrics-explorer.md) által hozzáadott videóban.

### <a name="platform-logs"></a>Platformnaplók

- [Összegyűjtheti és elemezheti az Azure-beli tevékenység naplóját Azure monitor](./platform/activity-log.md) – újraírhatja, hogy jobban megmagyarázza a begyűjtési tevékenység naplójának diagnosztikai beállításait.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

- [Azure-beli virtuális gépek figyelése Azure monitor](insights/monitor-vm-azure.md) -új cikkel.
- Gyors útmutató: az Azure-beli [virtuális gépek Azure monitor](learn/quick-monitor-azure-vm.md) frissítéssel való figyelése Azure monitor for VMS hozzáadásához.
- [Riasztások Azure monitor for VMS](insights/vminsights-alerts.md) – új cikk.
- [Azure monitor for VMS áttekintés](insights/vminsights-enable-overview.md) – az ügynök letöltési hivatkozásainak frissítése.

Általánosan elérhető általános frissítések Azure Monitor for VMs

- [Mi az Azure Monitor for VMs?](insights/vminsights-overview.md)
- [Azure Monitor for VMs (GA) – gyakori kérdések](insights/vminsights-ga-release-faq.md) 
- [Azure Monitor for VMs engedélyezése Azure Policy használatával](./insights/vminsights-enable-policy.md) 
- [A teljesítmény diagram Azure Monitor for VMs](insights/vminsights-performance.md)
- [Naplók lekérdezése Azure Monitor for VMs](insights/vminsights-log-search.md)
- [Alkalmazás függőségeinek megtekintése Azure Monitor for VMs](insights/vminsights-maps.md) 

### <a name="visualizations"></a>Vizualizációk

- [Adatok megjelenítése Azure monitorról](visualizations.md) – frissítve a View Designer tervezett elavulása megjegyzéssel.

## <a name="february-2020"></a>2020. február

### <a name="agents"></a>Ügynökök

Több frissítés a diagnosztikai bővítmény tartalmának újraírása részeként.

- [Az Azure monitoring-ügynökök áttekintése](platform/agents-overview.md) – átstrukturált táblázatok az egyes ügynökök egyedi funkcióinak jobb tisztázásához.
- [Azure Diagnostics bővítmény áttekintése](platform/diagnostics-extension-overview.md) – teljes újraírás.
- A [blob Storage használata az IIS-hez és a Table Storage](platform/diagnostics-extension-logs.md) -hoz a frissítéshez és a tisztasághoz Azure monitor általános újraírással kapcsolatos eseményekhez.
- [Telepítse és konfigurálja a Windows Azure Diagnostics bővítményt (wad)](platform/diagnostics-extension-windows-install.md) – új cikk. 
- [Windows Diagnostics bővítmény sémája](platform/diagnostics-extension-schema-windows.md) – átrendezve.
- [Adatok küldése a Windows Azure Diagnostics bővítményből az azure Event Hubsba](platform/diagnostics-extension-stream-event-hubs.md) – teljes mértékben újraírható és frissítve.
- [Diagnosztikai adatgyűjtés tárolása és megtekintése az Azure Storage-ban](../cloud-services/diagnostics-extension-to-storage.md) – teljes mértékben újraírható és frissíthető.
- [Log Analytics virtuálisgép-bővítmény a Windowshoz](../virtual-machines/extensions/oms-windows.md) – jobban tisztázza log Analytics ügynökkel való kapcsolatot.
- [Azure monitor virtuálisgép-bővítmény a Linux](../virtual-machines/extensions/oms-linux.md) rendszerhez – jobban tisztázza log Analytics-ügynökkel való kapcsolatot.

### <a name="application-insights"></a>Application Insights

- [A kapcsolatok karakterláncai az Azure Application Insightsban](app/sdk-connection-string.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat

#### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz

- [Azure Active Directory integrálása az Azure Kubernetes szolgáltatással](../aks/azure-ad-integration-cli.md) – új Megjegyzés a Kubernetes RBAC-kompatibilis fürtöket támogató ügyfélalkalmazás létrehozásához a tárolók Azure monitor támogatásához.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez

- [Azure monitor for VMS (GA) gyakori kérdések](insights/vminsights-ga-release-faq.md) – a teljesítményadatok tárolási módjának módosítása.

#### <a name="office-365"></a>Office 365

- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – frissített elavulás dátuma.


### <a name="logs"></a>Naplók

- Azure Monitor-új cikkben [található naplók optimalizálása](log-query/query-optimization.md) .
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – továbbfejlesztett minta-lekérdezések, amelyek segítenek a használat megértésében.

### <a name="metrics"></a>Mérőszámok

- [Azure monitor a platform metrikái exportálhatók a diagnosztikai beállítások](platform/metrics-supported-export-diagnostic-settings.md) – hozzáadott szakasz a nullák és a nulla értékek viselkedésének változása esetén.

### <a name="visualizations"></a>Vizualizációk

Több új cikk a Designer for munkafüzetek átalakítási útmutatójában.

- [Azure monitor a Designer és a munkafüzetek közötti áttérési útmutató](platform/view-designer-conversion-overview.md) – új cikk.
- [Azure monitor tervező átalakítása munkafüzetekbe konvertálási beállítások](platform/view-designer-conversion-options.md) – új cikk.
- [Azure monitor megtekintheti a tervezőt munkafüzetekhez csempe-konverziók](platform/view-designer-conversion-tiles.md) – új cikk.
- [Azure monitor megtekintheti a tervezőt a munkafüzetek átalakításának összegzése és a hozzáférés](platform/view-designer-conversion-access.md) – új cikk alapján.
- [Azure monitor tervező átalakítása munkafüzetekhez gyakori feladatok konvertálása](platform/view-designer-conversion-tasks.md) – új cikk.
- [Azure monitor tervező átalakítása munkafüzetek átalakítására – példák](platform/view-designer-conversion-examples.md) – új cikk.

## <a name="january-2020"></a>2020. január

### <a name="general"></a>Általános

- [Mi figyeli a Azure Monitor?](monitor-reference.md) – Új cikk.

### <a name="agents"></a>Ügynökök

- [A naplófájlok adatainak összegyűjtése az Azure log Analytics agenttel – a](platform/log-analytics-agent.md) hálózati tűzfalra vonatkozó követelmények tábla frissítése.

### <a name="alerts"></a>Riasztások

- [Műveleti csoportok létrehozása és kezelése a Azure Portalban](platform/action-groups.md) – a már nem szükséges v2-függvények eltávolításának beállítása.
- [Metrikai riasztás létrehozása Resource Manager-sablonnal](platform/alerts-metric-create-templates.md) – példa a *ignoreDataBefore* paraméterhez.  A többszörös feltételi szabályokkal kapcsolatos korlátozásokat adott meg.
- [Log Analytics riasztás használata REST API](platform/api-alerts.md) – JSON-példa kijavítva.

### <a name="application-insights"></a>Application Insights

- Az [Application Insights által használt IP-címek és a log Analytics](app/ip-addresses.md) – a rendelkezésre állási teszt szakasz frissítése a bejövő portszabály hozzáadásával az Azure hálózati biztonsági csoportokat használó forgalom engedélyezéséhez.
- [Az Azure Application Insights Profiler kapcsolatos problémák elhárítása](app/profiler-troubleshooting.md) – frissített általános hibaelhárítás.
- [Telemetria mintavételezés az Azure Application Insightsban](app/sampling.md) – frissített és átstrukturált, hogy javítsa az olvashatóságot az ügyfelek visszajelzései alapján.

### <a name="data-security"></a>Adatbiztonság

- [Azure monitor ügyfél által felügyelt kulcs konfigurálása](platform/customer-managed-keys.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat

#### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz

- [Azure monitor konfigurálása a containers Agent adatgyűjtéshez](insights/container-insights-agent-config.md) – további részletek az ügynök frissítéséhez az Azure Red Hat OpenShift, és további információkkal bővült az ügynök verziófrissítésének módszerei.
- [Teljesítmény-riasztások létrehozása a tárolók Azure monitor számára](./insights/container-insights-log-alerts.md) – felülvizsgált információk és frissített lépések a munkaterületen tárolt teljesítményadatok riasztásának létrehozásához munkaterületen belüli riasztások használatával.
- [Kubernetes-figyelés a Azure monitor for containers szolgáltatással](insights/container-insights-analyze.md) – a Windows Kubernetes-fürtök támogatásáról szóló áttekintő cikk és a elemzésre vonatkozó cikk frissítése.
- [Azure Red Hat OpenShift-fürtök konfigurálása az Azure monitor for containers](insights/container-insights-azure-redhat-setup.md) szolgáltatással – további részletek az ügynök frissítéséhez az Azure Red Hat OpenShift, és további információkkal bővült az ügynök verziófrissítésének módszerei.
- [Hibrid Kubernetes-fürtök konfigurálása Azure monitor for containers](insights/container-insights-hybrid-setup.md) használatával – frissítve a biztonságos port hozzáadásának támogatásával: a 10250 a Kubelet cAdvisor.
- [A containers agent Azure monitor kezelése – az](insights/container-insights-manage-agent.md) Azure Red Hat OpenShift és a metrikák leselejtezésének a viselkedésével és konfigurálásával kapcsolatos frissített részletek a más típusú Kubernetes-fürtökhöz képest.
- [Azure monitor konfigurálása a containers Prometheus-integrációhoz](insights/container-insights-prometheus-integration.md) – frissített részletek az Azure Red Hat OpenShift származó metrika-leselejtezési viselkedéssel és konfigurációval kapcsolatban, más típusú Kubernetes-fürtökhöz képest.
- A [tárolók Azure monitorának frissítése a metrikák számára](insights/container-insights-update-metrics.md) – frissített részletek az Azure Red Hat OpenShift a metrika-lekaparás működésével és konfigurálásával, valamint más típusú Kubernetes-fürtökhöz képest.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez

- [Azure monitor for VMS (GA) – gyakori kérdések](insights/vminsights-ga-release-faq.md) – további információk a munkaterület és az ügynökök új verzióra való frissítéséről.

#### <a name="office-365"></a>Office 365

- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – további részletek és gyakori kérdések az Office 365-megoldás Azure sentinelben való áttelepítéséről. A bevezetési szakasz el lett távolítva.

### <a name="logs"></a>Naplók

- [Log Analytics munkaterületek kezelése Azure monitor](platform/manage-access.md) – nem műveletekre vonatkozó frissítések.
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – az adatmennyiség kiszámításának pontosítása a díjszabási modell szakaszban.
- [Azure Resource Manager-sablonokkal létrehozhat és konfigurálhat egy log Analytics-munkaterületet](./samples/resource-manager-workspace.md) frissített sablont új díjszabási szintek használatával.

### <a name="platform-logs"></a>Platformnaplók

- [Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal – Azure monitor](./platform/activity-log.md) – további információ a módosult tulajdonságokkal kapcsolatban.
- [Exportálja az Azure-tevékenység naplóját](./platform/activity-log.md#legacy-collection-methods) – frissítve a felhasználói felület változásaihoz. 

## <a name="december-2019"></a>2019. december

### <a name="agents"></a>Ügynökök

- [Linux rendszerű számítógépek Összekapcsolásának Azure monitor](platform/agent-linux.md) – új cikk.

### <a name="alerts"></a>Riasztások

- [Metrikus riasztás létrehozása Resource Manager-sablonnal](platform/alerts-metric-create-templates.md) – példa az egyéni metrikára.
- Dinamikus [küszöbértékekkel rendelkező riasztások létrehozása Azure monitor](platform/alerts-dynamic-thresholds.md) – hozzáadott szakasz a dinamikus küszöbértékű diagramok értelmezéséhez.
- [A riasztások és az értesítések monitorozásának áttekintése az Azure-ban](platform/alerts-overview.md) – frissített Resource Graph-lekérdezés.
- A [metrikus riasztások támogatott erőforrásai Azure monitor](platform/alerts-metric-near-real-time.md) – a metrikák és a dimenziók frissítése támogatott.
- [Váltás az örökölt log Analytics riasztások API-ból az új Azure-riasztások API-ba](platform/alerts-log-api-switch.md) – Megjegyzés hozzáadva a módosított riasztás nevéhez.
- [Ismerje meg, hogyan működnek a metrikus riasztások Azure Monitorban.](platform/alerts-metric-overview.md) – Támogatott erőforrástípusok hozzáadása a nagy léptékű figyeléshez.

### <a name="application-insights"></a>Application Insights

- [Application Insights a Worker Service-alkalmazásokhoz (nem HTTP-alkalmazásokhoz)](app/worker-service.md) – az alapértelmezett naplózási szint hozzáadva a C#-kódhoz. Frissített csomag-hivatkozási verzió.
- [ApplicationInsights.config hivatkozás – Azure](app/configuration-with-applicationinsights-config.md) által frissített mintakód.
- [Az Azure Application Insights automatizálása a PowerShell](app/powershell.md) használatával – frissítés Resource Manager-sablonra.
- [Hozzon létre egy új Azure Application Insights-erőforrást](app/create-new-resource.md) , amely a globálisan egyedi névvel lett hozzáadva.
- [Diagnosztizálás élő metrikastream – Azure Application Insights](app/live-stream.md) – frissített ASP.net Core SDK-verzióra vonatkozó követelmény.
- [Application Insights](app/eventcounters.md) – frissített kategória és tábla customMetrics.
- [Ismerje meg a Java-nyomkövetési naplókat az Azure Application Insights](app/java-trace-logs.md) – a Java-ügynök naplózási küszöbértékének konfigurációját.
- Az [Application Insights által használt IP-címek és a log Analytics](app/ip-addresses.md) a élő METRIKASTREAM frissített IP-címei.
- Az [Azure app Services teljesítményének figyelése](app/azure-web-apps.md) – a ASP.net Core 3,0 támogatásának támogatása. 
- [Python-alkalmazások figyelése Azure monitor (előzetes verzió)](app/opencensus-python.md) – a OpenCensus Python-séma az Azure-ba való hozzárendelésének pontosítása. Séma figyelése
- [Kibocsátási megjegyzések az Azure Application Insightshoz](app/release-notes.md) – megjegyzések a régebbi kiadásokhoz.
- [Telemetria-csatornák Az Azure-ban Application Insights](app/telemetry-channels.md) – az elvetett adatmennyiségnek az elveszett kapcsolat hosszabb időszaka során történő frissített időtartama.
- [Telemetria mintavételezés az Azure Application Insightsban](app/sampling.md) – javított kódrészlet az egyéni TelemetryInitializer.
- A [Java-webprojektek Application Insightsának hibáinak megoldása](app/java-troubleshoot.md) – eltávolított utasítás, amely nem támogatja a függőségi gyűjteményt a JDK 9-ben.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat

- [Azure monitor a tárolók gyakori kérdéseivel](./faq.md) kapcsolatban – kérdésekkel bővült a képek és a nevek mezői.
- [Azure SQL Analytics megoldás a Azure monitor](insights/azure-sql.md) -frissítve adatbázisban vár felügyelt példányok támogatása.
- [Azure monitor konfigurálása a containers Agent adatgyűjtéshez](insights/container-insights-agent-config.md) – hozzáadott beállítás a enrich_container_logshoz.
- [Hibrid Kubernetes-fürtök konfigurálása Azure monitor for containers](insights/container-insights-hybrid-setup.md) – hibaelhárítás szakasz.
- [Active Directory replikáció állapotának figyelése Azure monitor](insights/ad-replication-status.md) -.NET-keretrendszerre vonatkozó előfeltétel-frissítéssel.
- [Network Performance monitor megoldás az Azure-ban](insights/network-performance-monitor.md) – támogatott régiók.
- [A Active Directory-környezet optimalizálása Azure monitor](insights/ad-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [A SQL Server-környezet optimalizálása Azure monitor](insights/sql-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [Optimalizálja System Center Operations Manager környezetét az Azure log Analytics](insights/scom-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [Támogatott kapcsolatok az Azure Log Analytics it-szolgáltatásmenedzsmenti csatolóával](platform/itsmc-connections.md) – új York-i előfeltétel-ügyfél-azonosító és az ügyfél titkos kulcsa.

### <a name="logs"></a>Naplók

- [Az Azure log Analytics munkaterület törlése és helyreállítása](platform/delete-workspace.md) – PowerShell-metódus hozzáadva.
- [Megtervezheti a Azure monitor naplók üzembe helyezését](platform/design-logs-deployment.md) – a munkaterületek terhelési arányának növelését.

### <a name="metrics"></a>Mérőszámok

- [Azure monitor a platform metrikái exportálhatók a diagnosztikai beállítások használatával](platform/metrics-supported-export-diagnostic-settings.md) – új cikk.

### <a name="platform-logs"></a>Platformnaplók

Több cikk is frissült a tartalom átszervezésének részeként a platform naplóiban a diagnosztikai beállítások használatával a műveletnapló konfigurálására szolgáló új funkció alapján.

- [Azure-beli erőforrás-naplók archiválása a Storage-fiókba](./platform/resource-logs.md#send-to-azure-storage)
- [Azure Activity log esemény sémája](platform/activity-log-schema.md)
- [Azure Monitor szolgáltatási korlátok](service-limits.md)
- [Azure-beli tevékenység-naplók összegyűjtése és elemzése Log Analytics munkaterületen](./platform/activity-log.md)
- [Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal (előzetes verzió) – Azure Monitor](./platform/activity-log.md)
- [Azure-beli tevékenységek naplóinak begyűjtése egy Log Analytics munkaterületre az Azure-bérlők között](./platform/activity-log.md)
- [Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Diagnosztikai beállítás létrehozása az Azure-ban Resource Manager-sablon használatával](./samples/resource-manager-diagnostic-settings.md)
- [Diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](platform/diagnostic-settings.md)
- [Az Azure-tevékenység naplójának exportálása](./platform/activity-log.md#legacy-collection-methods)
- [Az Azure-platform naplóinak áttekintése](platform/platform-logs-overview.md)
- [Azure monitoring-adatstreamek továbbítása az Event hub szolgáltatásba](platform/stream-monitoring-data-event-hubs.md)
- [Azure platform-naplók továbbítása egy Event hubhoz](./platform/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Rövid útmutatók és oktatóanyagok

- [Metrikai diagram létrehozása Azure monitor](learn/tutorial-metrics-explorer.md) – új cikkben.
- [Erőforrás-naplók összegyűjtése Azure-erőforrásokból és Azure monitor](learn/tutorial-resource-logs.md) -új cikk elemzése.
- [Azure-erőforrás figyelése Azure monitor](learn/quick-monitor-azure-resource.md) -új cikkel.
   
## <a name="next-steps"></a>Következő lépések

- Ha szeretne hozzájárulni Azure Monitor dokumentációhoz, tekintse meg a [docs közreműködői útmutatóját](/contribute/).