---
title: Az Azure Monitor dokumentációjának újdonságai
description: A Azure Monitor dokumentációjának frissítése havonta frissül.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: b21e5ea8e25844e3e025915b0b9f15162c642f25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713439"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Az Azure Monitor dokumentációjának újdonságai

Ez a cikk felsorolja Azure Monitor új vagy jelentős mértékben frissített cikkeket. A rendszer minden hónap első hetében frissíti az előző hónapból származó cikkek frissítéseinek befoglalásával.

## <a name="january-2021"></a>2021. január 

### <a name="general"></a>Általános kérdések 
- [Azure monitor GYIK](faq.md) – a Application Insights eszköz adataihoz hozzáadott bejegyzés.
### <a name="agents"></a>Ügynökök  
- [Windows esemény-nyomkövetés-(ETW-) események gyűjtése az analysis Azure monitor-naplókban](./agents/data-sources-event-tracing-windows.md) – új cikk.
- [Adatgyűjtési szabályok a Azure monitor (előzetes verzió)](./agents/data-collection-rule-overview.md) – a PowerShell-és CLI-mintákra mutató hivatkozásokkal bővült.

### <a name="alerts"></a>Riasztások  
- [Konfigurálja az Azure-t a ITSM-eszközök biztonságos exportálással való összekapcsolására](./alerts/itsm-connector-secure-webhook-connections-azure-configuration.md) – új cikk.
- [Az összekötő állapotával kapcsolatos hibák a ITSMC-irányítópulton](./alerts/itsmc-dashboard-errors.md) – új cikk.
- [Vizsgálja meg a hibákat a ITSMC-irányítópult használatával](./alerts/itsmc-dashboard.md) – új cikk.
- [Azure metric-riasztások](./alerts/alerts-troubleshoot-metric.md) – a dinamikus küszöbértékekhez hozzáadott szakaszt tartalmaz.
- [It-szolgáltatásmenedzsmenti csatoló kapcsolatos problémák elhárítása](./alerts/itsmc-troubleshoot-overview.md) – új cikk.

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
- [Azure monitor ügyfél által felügyelt kulcs](./logs/customer-managed-keys.md) – felhasználóhoz rendelt felügyelt identitás bevezetése.
- A [Azure monitor a dedikált fürtöket naplózza](./logs/logs-dedicated-clusters.md) – frissítette a visszajelzési kódot.
- [Több szolgáltatás lekérdezése – Azure monitor és Azure adatkezelő (előzetes verzió)](/azure/azure-monitor/platform/azure-data-explorer-monitor-cross-service-query) – új cikk.

### <a name="metrics"></a>Mérőszámok
- [Azure monitor metrikák metrikáinak összesítése és megjelenítése](./essentials/metrics-aggregation-explained.md) – új cikk.

### <a name="platform-logs"></a>Platform naplói
- [Azure monitor erőforrás-naplók támogatott szolgáltatások és kategóriák](./essentials/resource-logs-categories.md) – új cikk.

### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzetek adatforrásai – az](./visualize/workbooks-data-sources.md) egyesítés és a változás elemzése is megtörténik.


## <a name="december-2020"></a>2020. december

### <a name="general"></a>Általános kérdések
- [Azure monitor ügyfél által felügyelt kulcshoz](logs/customer-managed-keys.md) hozzáadott hibaüzenetek.
- Az Event hub-integrációval Azure Monitor hozzáadott szakasszal [integrált partnerek](partners.md) .

### <a name="agents"></a>Ügynökök
- [Erőforrás-lekérdezés az Azure Adatkezelő Azure monitor](logs/azure-monitor-data-explorer-proxy.md) -új cikk használatával.
- [Az Azure monitoring Agent áttekintése – az](agents/agents-overview.md) Oracle 8 támogatása.

### <a name="alerts"></a>Riasztások
- [Azure metric-riasztások](alerts/alerts-troubleshoot-metric.md) – hibaelhárítás a dinamikus küszöbértékekhez.
- [Log Analytics](alerts/itsmc-definition.md) – új cikk it-szolgáltatásmenedzsmenti csatoló.
- [It-szolgáltatásmenedzsmenti csatoló áttekintése](alerts/itsmc-overview.md) – átstrukturált hibaelhárítási információk.
- [A Cherwell it-szolgáltatásmenedzsmenti csatoló-új cikkel való összekapcsolásához](alerts/itsmc-connections-cherwell.md) .
- [A it-szolgáltatásmenedzsmenti csatoló új cikkel való összekapcsoláshoz](alerts/itsmc-connections-provance.md) .
- [A SCSM it-szolgáltatásmenedzsmenti csatoló-új cikkel való összekapcsolásához](alerts/itsmc-connections-scsm.md) .
- [A ServiceNow it-szolgáltatásmenedzsmenti csatoló-új cikkel való összekapcsolásához](alerts/itsmc-connections-servicenow.md) .
- [ServiceNow-szinkronizálási problémák manuális javítása](alerts/itsmc-resync-servicenow.md) – átstrukturált hibaelhárítási információk.




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
- [Jelentések a Container bepillantást](insights/container-insights-reports.md) tartalmazó új cikkben.

### <a name="logs"></a>Naplók
- [Azure monitor naplók dedikált fürtök](logs/logs-dedicated-clusters.md) – új automatizált parancsok, a leválasztási és eltávolítási módszerek, valamint a hibaelhárítás.
- [Azure monitor és az Azure adatkezelő (előzetes verzió) közötti több szolgáltatás lekérdezése](logs/azure-data-explorer-monitor-cross-service-query.md) – új cikk.
- [Log Analytics munkaterület-adatexportálás Azure monitor (előzetes verzió)](logs/logs-data-export.md) – ARM-sablonok hozzáadása.

### <a name="metrics"></a>Mérőszámok
- [Az Azure Metrikaböngésző speciális funkciói](essentials/metrics-charts.md) – további információ az erőforrás-hatókör választóról.
- [Több erőforrás megtekintése Metrikaböngésző](essentials/metrics-dynamic-scope.md) – új cikkben.

### <a name="networks"></a>Hálózatok
- [Azure hálózatkezelési elemzési megoldás Azure monitor](insights/azure-networking-analytics.md) – további információ a hálózati elemzések munkafüzetéről.

### <a name="virtual-machines"></a>Virtual Machines
- [Hibrid környezetek Azure monitor engedélyezése](vm/vminsights-enable-hybrid.md) – a függőségi ügynök új verziója.


### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzet-térképi vizualizációk](visualize/workbooks-map-visualizations.md) – új cikk.
- [A Azure monitor-munkafüzetek saját tárterületet hozhatnak](visualize/workbooks-bring-your-own-storage.md) – új cikk.


## <a name="november-2020"></a>2020. november

### <a name="general"></a>Általános kérdések
- [Azure monitor szolgáltatási korlátok](service-limits.md) – frissítve az Azure arc támogatásához.

### <a name="agents"></a>Ügynökök
- [Az Azure monitoring Agent áttekintése](agents/agents-overview.md) – az Azure arc támogatásának frissítése.
- [A Azure monitor-ügynök telepítése](agents/azure-monitor-agent-install.md) – új cikk.
- [Azure monitor-ügynök áttekintése](agents/azure-monitor-agent-overview.md) – az Azure arc támogatásának frissítése.
- [Resource Manager-sablon minták az ügynökökhöz](agents/resource-manager-agent.md) – frissítve az Azure arc támogatásához.

### <a name="alerts"></a>Riasztások
- [Hozzon létre és kezeljen műveleti csoportokat a](alerts/action-groups.md) webhookok Azure Portal hozzáadott forrás IP-címeiben.

### <a name="application-insights"></a>Application Insights
- A Java Code unapplication [monitoring Azure monitor Application Insights](app/java-in-process-agent.md) – példa a konfigurációra.
- A [Application Insights JavaScript SDK-hoz tartozó beépülő modult](app/javascript-react-plugin.md) a Reports (reagáló hookok használata) című szakaszban találja.
- [Frissítés Application Insights Java 2. x SDK-ról](app/java-standalone-upgrade-from-2x.md) – új cikk.
- [Kibocsátási megjegyzések a Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](app/snapshot-collector-release-notes.md) – új cikkhez.

### <a name="autoscale"></a>Automatikus méretezés
- [Ismerkedjen meg az Azure](autoscale/autoscale-get-started.md) -ra épülő, az autoscale egy másik régióba való áthelyezésével foglalkozó témakörben.

### <a name="data-collection"></a>Adatgyűjtés
- [Az adatgyűjtési szolgáltatás konfigurálása a Azure monitor ügynökhöz (előzetes verzió)](agents/data-collection-rule-azure-monitor-agent.md) – az Azure arc támogatásának frissítése.
- [Adatgyűjtési szabályok a Azure monitor (előzetes verzió)](agents/data-collection-rule-overview.md) – frissítve az Azure arc támogatásához.
- [Resource Manager-sablonok az adatgyűjtés szabályaihoz](agents/resource-manager-data-collection-rules.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat
- [Csatlakoztassa az Azure-t a ITSM-eszközökhöz](alerts/it-service-management-connector-secure-webhook-connections.md) az ServiceNow-hez való csatlakozáshoz szükséges biztonságos exportálási szakasz használatával.

### <a name="logs"></a>Naplók
- [Log Analytics és Excel](logs/log-excel.md) – új cikk integrálása.
- [Log Analytics adatbiztonság](logs/data-security.md) – további biztonsági funkciók című szakasz.
- [Log Analytics integráció Power bi](logs/log-powerbi.md) -új cikkel.
- [Azure monitor rekordok szabványos oszlopai](logs/log-standard-columns.md) – _SubscriptionId oszlop hozzáadva.

Új és frissített cikkek a napló lekérdezési tartalmának átszervezéséről.

- [Log Analytics-oktatóanyag](logs/log-analytics-tutorial.md)
- [Lekérdezések naplózása Azure Monitor](logs/log-query-overview.md)
- [A Azure Monitor Log Analytics áttekintése](logs/log-analytics-overview.md)
- [Példák az Azure Adatkezelő és Azure Monitor lekérdezésére](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Oktatóanyag: Kusto-lekérdezések használata az Azure Adatkezelő és Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

- A [VM-információk áttekintésének engedélyezése](vm/vminsights-enable-overview.md) – támogatott régiók hozzáadása.

Új cikkek a virtuális gépek bepillantást a vendég állapotára (előzetes verzió)

- [A VM bepillantást a vendég állapotára (előzetes verzió)](vm/vminsights-health-overview.md)
- [A virtuális gépekkel kapcsolatos vendég állapottal kapcsolatos riasztások (előzetes verzió)](vm/vminsights-health-alerts.md)
- [A figyelés konfigurálása a VM-beli betekintő Guest Health szolgáltatásban (előzetes verzió)](vm/vminsights-health-configure.md)
- [Az adatgyűjtési szabályok (előzetes verzió) használatával konfigurálhatja a virtuális gépek elemzését a vendég állapotában](vm/vminsights-health-configure-dcr.md)
- [A virtuális gépek bepillantást tevő vendég állapotának engedélyezése (előzetes verzió)](vm/vminsights-health-enable.md)
- [A virtuális gépekkel kapcsolatban észlelt vendég állapotának hibáinak megoldása (előzetes verzió)](vm/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>2020. október

### <a name="general"></a>Általános kérdések
- [Azure monitor API-nyugdíjazás](logs/operationalinsights-api-retirement.md) – új cikk.

### <a name="agents"></a>Ügynökök
- [Az ügynökök Azure monitor által felügyelt](monitor-reference.md) szakasza.

### <a name="alerts"></a>Riasztások
- [Műveleti csoportokat hozhat létre és kezelhet a](alerts/action-groups.md) szolgáltatás címkéje Azure Portal hozzáadott szakaszában.
- [Resource Manager-sablon – minták metrikus riasztásokhoz](alerts/resource-manager-alerts-metric.md) – a hozzáadott tartalom egyeztetési paraméter-és tesztelési helyei.
- [Azure metrikus riasztások hibaelhárítása](alerts/alerts-troubleshoot-metric.md) – a szabályok konfigurálásának ajánlott gyakorlata.

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
- [Ismerkedjen meg az Azure](autoscale/autoscale-get-started.md) -ra épülő autoskálázással az autoskálázás más régióba való áthelyezésével foglalkozó szakaszban.

### <a name="containers"></a>Tárolók
- [A PV-figyelés konfigurálása a Container bepillantásokkal](containers/container-insights-persistent-volumes.md) – új cikk.
- [A Container bepillantási ügynök kezelése – az](containers/container-insights-manage-agent.md) Azure arc-kompatibilis Kubernetes-fürt támogatása hozzáadva.
- [Metrikus riasztások a Container bepillantást](containers/container-insights-metric-alerts.md) követően – az Azure arc-kompatibilis Kubernetes-fürt támogatása hozzáadva.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat
- [It-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure monitor](alerts/it-service-management-connector-secure-webhook-connections.md) – a ServiceNow-ben hozzáadott szakasz.

### <a name="logs"></a>Naplók
- [Adatok archiválása log Analytics munkaterületről az Azure Storage](logs/logs-export-logic-app.md) -ba a Logic app – új cikk használatával.
- [Log Analytics munkaterület-adatok exportálása Azure monitorban (előzetes verzió)](logs/logs-data-export.md) – az Event hub Rest-kérelméhez hozzáadott minta törzs.
- [Azure monitor naplók használatának és költségeinek kezelése](logs/manage-cost-storage.md) – a Azure monitor naplók és a Azure Security Center számlázás közötti kapcsolattal kapcsolatos információk. A csomópontok számának és a csomópontok díjszabási szintjeinek használatával hozzáadott lekérdezés. 
- [Log Analytics munkaterület állapotának figyelése Azure monitor](logs/monitor-workspace.md) – új cikk.
- [Adatlekérdezés Azure monitor az Azure adatkezelő (előzetes verzió) használatával](logs/azure-data-explorer-monitor-proxy.md) – új cikk.
- [Azure monitor exportált adatok lekérdezése az Azure adatkezelő (előzetes verzió) használatával](logs/azure-data-explorer-query-storage.md) – új cikk.

### <a name="networks"></a>Hálózatok
- [Hálózatok Azure monitor előzetes](insights/network-insights-overview.md) verzió – hibaelhárítási szakasz. További szakasz a kapcsolathoz.

### <a name="platform-logs"></a>Platformnaplók
- [Azure Activity log esemény sémája](essentials/activity-log-schema.md) – a súlyossági szintek leírását adja meg.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
- [Change Analysis in Azure monitor for VMS](vm/vminsights-change-analysis.md) – új cikk.
- [Azure monitor for VMS-áttekintés engedélyezése](vm/vminsights-enable-overview.md) – támogatott régiók hozzáadva.
- A [metrikák tároló](containers/container-insights-update-metrics.md) -információinak frissítése – az Azure arc-kompatibilis Kubernetes-fürt támogatása.



## <a name="september-2020"></a>2020. szeptember

### <a name="general"></a>Általános kérdések
- [Azure monitor GYIK](faq.md) – további szakasz a OpenTelemetry.

### <a name="agents"></a>Ügynökök
- [Azure monitor ügynök áttekintése](agents/azure-monitor-agent-overview.md) – az új ügynökre való áttéréshez szükséges döntési tényezők.
- [Az Azure monitoring Agent áttekintése – a](agents/agents-overview.md) Windows 10 támogatása.

### <a name="alerts"></a>Riasztások
- [Log-riasztás létrehozása Azure Resource Manager sablonnal](alerts/alerts-log-create-templates.md) – új cikk.
- [Hibaelhárítás az Azure metrikus riasztások](alerts/alerts-troubleshoot-metric.md) számára – az ARM-sablon exportálása a metrika riasztási szabályhoz című szakasza.

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
- Az [Azure arc-kompatibilis Kubernetes-fürt konfigurálása a tároló](containers/container-insights-enable-arc-enabled-clusters.md) -megállapításokkal – további útmutatást nyújt az egyszerű szolgáltatásnév használatával történő figyelés engedélyezéséhez.
- [Üzembe helyezés & hPa-metrikák a Container bepillantásokkal](containers/container-insights-deployment-hpa-metrics.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat
- [Az Azure cache](insights/redis-cache-insights-overview.md) -hez készült Azure monitor Redis – eltávolított előnézet megjelölése.
- [Azure monitor hálózatok számára (előzetes verzió) – a](insights/network-insights-overview.md) kapcsolat és a forgalom további részei.
- [It-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure monitor](alerts/it-service-management-connector-secure-webhook-connections.md) – új cikk.
- [IT-szolgáltatásmenedzsmenti csatoló Azure monitor](alerts/itsmc-connections.md) -Megjegyzés a Cherwell és az ITSM-integrációk terén.
- [Key Vault figyelése Azure Monitor Key Vault](insights/key-vault-insights-overview.md) -eltávolított előnézet megjelöléssel.

### <a name="logs"></a>Naplók
- [Lekérdezések naplózása Azure monitor log lekérdezésekben](logs/query-audit.md) – új cikk.
- [Azure monitor ügyfél által felügyelt kulcshoz](logs/customer-managed-keys.md) hozzáadott ügyfél-kulcstároló.
- [Azure monitor naplók dedikált fürtök](logs/logs-dedicated-clusters.md) – új cikk.
- [A Azure monitor naplók üzembe helyezésének megtervezése – a](logs/design-logs-deployment.md) méretezési és a betöltési mennyiségre vonatkozó korlátozás szakasz frissítése.
- [A lekérdezés hatókörének naplózása Azure Monitor log Analytics](logs/scope.md) -a alkalmazásban a munkaterület-alapú alkalmazások belefoglalása.
- [Naplók Azure monitor-a](logs/data-platform-logs.md) munkaterület-alapú alkalmazásokat tartalmazó frissítések.
- [A Azure monitor rekordok szabványos oszlopai – a](logs/log-standard-columns.md) frissítések, amelyek munkaterületen alapuló alkalmazásokat tartalmaznak.
- [Azure monitor szolgáltatás korlátai](service-limits.md) – a felhasználói lekérdezések szabályozásának frissített korlátai.
- [Az ügyfél által felügyelt Storage-fiókok használata Azure Monitor log Analyticsban – a](logs/private-storage.md) cikk átírása megtörténik.
- Az [Azure log Analytics-ben tárolt adatok megtekintése és elemzése](./logs/data-platform-logs.md) – a frissítések a munkaterület-alapú alkalmazások belefoglalásához szükségesek.


### <a name="platform-logs"></a>Platformnaplók
- [Azure Activity log esemény sémája – Azure monitor](essentials/activity-log-schema.md) – súlyossági szintek hozzáadása.
- [Resource Manager-sablonok – minták a diagnosztikai beállításokhoz](essentials/resource-manager-diagnostic-settings.md) – az Azure Storage-fiókhoz hozzáadott minta.

### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzet-diagram vizualizációi](visualize/workbooks-chart-visualizations.md) – új cikk.
- [Azure monitor munkafüzet összetett sávjának renderelése](visualize/workbooks-composite-bar.md) – új cikk.
- [Azure monitor munkafüzet Graph vizualizációk](visualize/workbooks-graph-visualizations.md) – új cikk.
- [Azure monitor munkafüzet Grid vizualizációk](visualize/workbooks-grid-visualizations.md) – új cikk.
- [Azure monitor munkafüzet mézes fésű vizualizációk](visualize/workbooks-honey-comb.md) – új cikk.
- [Azure monitor munkafüzet szövegének vizualizációi](visualize/workbooks-text-visualizations.md) – új cikk.
- [Azure monitor munkafüzet csempe vizualizációk](visualize/workbooks-tile-visualizations.md) – új cikk.
- [Azure monitor munkafüzet favizualizációi](visualize/workbooks-tree-visualizations.md) – új cikk.




## <a name="august-2020"></a>2020. augusztus

### <a name="general"></a>Általános kérdések

- A [Azure monitor által figyelt](monitor-reference.md) frissítések Azure monitor ügynök belefoglalásával.


### <a name="agents"></a>Ügynökök
- [Azure monitor ügynök áttekintése](agents/azure-monitor-agent-overview.md) – új cikk.
- [Azure monitor engedélyezése hibrid környezethez](vm/vminsights-enable-hybrid.md) – frissített függőségi ügynök verziója.
- [Az Azure monitoring-ügynökök áttekintése](agents/agents-overview.md) – Azure monitor ügynök és összevont operációs rendszer támogatási táblázata.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Új és frissített cikkek az ügynök tartalmának újrastrukturálása esetén
- [A VM-információk áttekintésének engedélyezése](vm/vminsights-enable-overview.md)
- [Log Analytics-ügynök telepítése Linux rendszerű számítógépeken](agents/agent-linux.md)
- [Log Analytics-ügynök telepítése Windows rendszerű számítógépeken](agents/agent-windows.md)
- [Log Analytics-ügynök áttekintése](agents/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights a JavaScript-webalkalmazásokhoz](app/javascript.md) – hozzáadott szakasz az ügyfél-kiszolgáló korrelációjának és konfigurációjának pontosítása a CORS korreláció érdekében.
- [Hozzon létre egy új Azure Monitor Application Insights munkaterület-alapú erőforrások](app/create-workspace-resource.md) által biztosított képességek a munkaterület-alapú alkalmazások számára.
- A [Application Insights és log Analytics által használt IP-címek](app/ip-addresses.md) az élő metrikák adatfolyamának frissített IP-címei.
- [Java-alkalmazások figyelése bármilyen környezetben – Azure Monitor Application Insights](app/java-in-process-agent.md) – a támogatott egyéni telemetria tartozó táblázat hozzáadva.
- [Natív reagáló beépülő modul a Application Insights JavaScript SDK](app/javascript-react-native-plugin.md) -hoz – új cikk.
- [A beépülő modul Application Insights JavaScript SDK](app/javascript-react-plugin.md) -hoz – új cikk.
- [Resource Manager-sablon minta Azure Function-alkalmazások létrehozásához Application Insights monitoring](app/resource-manager-function-app.md) – új cikk.
- [Resource Manager-sablonok az Azure app Services Web Apps és a Application Insights monitoring](app/resource-manager-web-app.md) – új cikk létrehozásához.
- [Használat elemzése az Azure Application Insights](app/usage-overview.md) – videó hozzáadva.

### <a name="autoscale"></a>Automatikus méretezés
- [Ismerkedjen meg az Azure-beli autoskálázással](autoscale/autoscale-get-started.md) , amely a app Service kifogástalan állapotú példányain való útválasztást ismerteti.

### <a name="data-collection"></a>Adatgyűjtés
- [Az adatgyűjtési szolgáltatás konfigurálása a Azure monitor-ügynökhöz (előzetes verzió)](agents/data-collection-rule-azure-monitor-agent.md) – új cikk.
- Adatgyűjtés [szabályai a Azure monitor (előzetes verzió)](agents/data-collection-rule-overview.md) – új cikk.


### <a name="containers"></a>Tárolók
- [Üzembe helyezés & hPa-metrikák a Container bepillantásokkal](containers/container-insights-deployment-hpa-metrics.md) – új cikk.

### <a name="insights"></a>Insights
- [Azure monitor figyelési megoldásai](insights/solutions.md) – új felhasználói felület frissítése.
- [Network Performance monitor megoldás az Azure-ban](insights/network-performance-monitor.md) – támogatott munkaterület-régiók.


### <a name="logs"></a>Naplók
- [Azure monitor GYIK](faq.md) – az adatok munkaterületről való törléséhez hozzáadott bejegyzés. Bejegyzés hozzáadva a 502-es és a 503-es válaszokhoz.
  - [A Azure monitor naplók üzembe helyezésének megtervezése – a](logs/design-logs-deployment.md) betöltési mennyiségre vonatkozó frissítési korlát szakasz.
- [Azure monitor naplók használatának és költségeinek kezelése – a](logs/manage-cost-storage.md) frissített használati lekérdezések hatékonyabbá teszi a lekérdezési formátumot.
- [A Azure monitor](logs/query-optimization.md) -specifikus értékek a teljesítménymutatók számára történő optimalizálása.
- [Resource Manager-sablonok – minták a diagnosztikai beállításokhoz](essentials/resource-manager-diagnostic-settings.md) – a napló lekérdezési naplóihoz hozzáadott minta.


### <a name="platform-logs"></a>Platformnaplók
- [Diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre való küldéséhez](essentials/diagnostic-settings.md) – a diagnosztikai beállításokhoz hozzáadott regionális követelmény.

### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzetek áttekintése](visualize/workbooks-overview.md) – videó hozzáadva.
- [Azure-munkafüzet sablonjának áthelyezése egy másik régióba](visualize/workbook-templates-move-region.md) – új cikk.
- [Azure-munkafüzetek áthelyezése egy másik régióba](visualize/workbooks-move-region.md) – új cikk.



## <a name="july-2020"></a>2020. július

### <a name="general"></a>Általános kérdések
- [Azure monitor üzembe helyezése](deploy-scale.md) – a virtuális gépek bevezetési tartalmának átstrukturálása.
- Az [Azure Private link használatával biztonságosan csatlakoztathatja a hálózatokat Azure monitor](logs/private-link-security.md) hozzáadott szakaszhoz a korlátok között.

### <a name="alerts"></a>Riasztások
- [Azure monitor riasztásokra vonatkozó műveleti szabályok](alerts/alerts-action-rules.md) – CLI-folyamatok hozzáadva.
- [Műveleti csoportok létrehozása és kezelése a Azure Portalban](alerts/action-groups.md) – frissítve a felhasználói felület változásainak megfelelően.
- [Példa lekérdezésekre Azure Monitor log Analytics](logs/example-queries.md) – új cikk.
- A riasztási szabály kvótájának Azure Monitor által hozzáadott szakasza [riasztások hibakeresése](alerts/alerts-troubleshoot-log.md) .
- Az [Azure metrika riasztásai](alerts/alerts-troubleshoot-metric.md) – a riasztási szabály hozzáadott szakasza egy olyan egyéni metrika esetében, amely még nincs kibocsátva.
- [Ismerje meg, hogyan működnek a metrikus riasztások Azure Monitorban.](alerts/alerts-metric-overview.md) -Javaslat hozzáadva az összesítési részletesség kiválasztásához.

### <a name="application-insights"></a>Application Insights
- [Az Azure Web App Extension kibocsátási megjegyzései – Application Insights](app/web-app-extension-release-notes.md) – új cikk.
- [Resource Manager-sablonok – minták Application Insights erőforrásokhoz](app/resource-manager-app-resource.md) – új cikk.
- Az [Azure Application Insights Profiler-vel kapcsolatos problémák elhárítása](app/profiler-troubleshooting.md) – megjegyzés a ASP.net Core-alkalmazások Azure app Service-on futó hiba a Profilerben. 

### <a name="containers"></a>Tárolók
- [Riasztások naplózása a Container bepillantásokból](containers/container-insights-log-alerts.md) – új cikk.
- [Metrikus riasztások a Container bepillantást](containers/container-insights-metric-alerts.md) követően – új cikk.

### <a name="logs"></a>Naplók
- [Azure monitor ügyfél által felügyelt kulcshoz](logs/customer-managed-keys.md) hozzáadott hibaüzenet és szakasz CMK-konfiguráció lekérdezésekhez.
- [Azure monitor http](logs/data-collector-api.md) -adatgyűjtő API – Python 3 minta hozzáadva.
- [Az](logs/query-optimization.md) allekérdezések használatakor a Azure monitor hozzáadott szakasz több adatvizsgálat elkerülését ismertető szakaszban található.
- [Oktatóanyag: Ismerkedés a log Analytics lekérdezésekkel](./logs/log-analytics-tutorial.md) – videó hozzáadva.

### <a name="platform-logs"></a>Platformnaplók
- [Diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre való küldéséhez](essentials/diagnostic-settings.md) – hozzáadott videó.
- [Resource Manager-sablon minták a Azure monitor](/resource-manager-samples.md) hozzáadott ARM-minta a naplók célhelyének típusával. 

### <a name="solutions"></a>Megoldások
- [Figyelési megoldások Azure monitor](insights/solutions.md) – CLI-folyamatok.
- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – megváltozott a nyugdíjazás dátuma.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

Új és frissített cikkek a virtuális gép elemzése tartalmának átszervezéséről

- [Mi az a VM-alapú adatfelismerés?](vm/vminsights-overview.md)
- [Log Analytics munkaterület konfigurálása a virtuális gépekhez](vm/vminsights-configure-workspace.md)
- [Linux rendszerű számítógépek összekapcsolásának Azure Monitor](agents/agent-linux.md)
- [Hibrid környezetek Azure Monitorának engedélyezése](vm/vminsights-enable-hybrid.md)
- [Azure Monitor engedélyezése egyetlen virtuális gép vagy virtuálisgép-méretezési csoport számára a Azure Portal](vm/vminsights-enable-portal.md)
- [A virtuális gépek bepillantást tesznek a Azure Policy használatával](./vm/vminsights-enable-policy.md)
- [A VM-információk áttekintésének engedélyezése](vm/vminsights-enable-overview.md)
- [VIRTUÁLIS gépekkel való bepillantások engedélyezése a PowerShell használatával](vm/vminsights-enable-powershell.md)
- [A virtuális gépekkel való bepillantások engedélyezése Resource Manager-sablonok használatával](vm/vminsights-enable-resource-manager.md)
- [VIRTUÁLIS gépekkel való bepillantások engedélyezése a PowerShell vagy a sablonok használatával](./vm/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Vizualizációk
- [Log Analytics irányítópult-vizualizációk frissítése](logs/dashboard-upgrade.md) – frissített frissítési sebesség.
- [Adatok megjelenítése Azure monitor](visualizations.md) által hozzáadott videóból.


## <a name="june-2020"></a>2020. június

### <a name="general"></a>Általános kérdések
- [Azure monitor üzembe helyezése](deploy-scale.md) – új cikk.
- [Azure monitor ügyfél által felügyelt kulcs](logs/customer-managed-keys.md) – frissített billingtype tulajdonság. PowerShell-parancsok hozzáadva.

### <a name="agents"></a>Ügynökök
- [Log Analytics Agent áttekintése](agents/log-analytics-agent.md) – a Python 2 követelményének hozzáadása.

### <a name="alerts"></a>Riasztások
- [Riasztási szabályok vagy műveleti szabályok frissítése, ha a célként megadott erőforrás egy másik Azure-régióba kerül át](alerts/alerts-resource-move.md) – új cikk.
- [Azure metric-riasztások hibaelhárítása](alerts/alerts-troubleshoot-metric.md) – új cikk.
- [Riasztások hibaelhárítása Azure monitor](alerts/alerts-troubleshoot-metric.md) – új cikk.
  
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
- [A hibrid Kubernetes-fürthöz tartozó új szakasz figyelésének leállítása](containers/container-insights-optout-hybrid.md) az ív-kompatibilis Kubernetes.
- [Az Azure arc-kompatibilis Kubernetes-fürt konfigurálása a Container bepillantásokkal](containers/container-insights-enable-arc-enabled-clusters.md) – új cikk.
- [Konfigurálja az Azure Red Hat OpenShift v4. x-et a Container bepillantásokkal](containers/container-insights-azure-redhat4-setup.md) – frissített előfeltételek.
- [Tároló-elemzések élő adatainak beállítása (előzetes verzió)](containers/container-insights-livedata-setup.md) – a funkció nem érhető el az Azure USA Government szolgáltatásban.

### <a name="insights"></a>Insights
- [Gyakori kérdések – Network Performance monitor megoldás az Azure-ban](insights/network-performance-monitor-faq.md) – ExpressRoute-figyelő – gyakori kérdések.

### <a name="logs"></a>Naplók
- [Az Azure log Analytics-munkaterület törlése és helyreállítása](logs/delete-workspace.md) – PowerShell-parancs hozzáadva. Frissített hibaelhárítás.
- [Log Analytics munkaterületek kezelése Azure monitor](logs/manage-access.md) – példa a nem engedélyezett táblákra az Azure RBAC szakaszban.
- [Azure monitor naplók használatának és költségeinek kezelése](logs/manage-cost-storage.md) – az adatméret számításával kapcsolatos további részletek. Frissült az adatmennyiség-riasztások konfigurálása. Az Azure Sentinel által összegyűjtött biztonsági adatok részletei. Az adatsapka pontosítása.
- [Azure monitor naplók használata a Azure Logic apps és a Power automatizáló](logs/logicapp-flow-connector.md) által hozzáadott összekötő-korlátokkal.

### <a name="metrics"></a>Mérőszámok
- [Azure monitor támogatott mérőszámok erőforrástípus szerint](essentials/metrics-supported.md) – frissített SQL Server metrikák.


### <a name="platform-logs"></a>Platformnaplók

- [Resource Manager-sablonok – minták a diagnosztikai beállításokhoz](essentials/resource-manager-diagnostic-settings.md) – javítás a műveletnapló diagnosztikai beállításához.
- [Azure-tevékenység naplójának küldése log Analytics munkaterületre Azure Portal](essentials/quick-collect-activity-log-portal.md) – új cikk használatával.
- [Azure-tevékenység naplójának küldése log Analytics munkaterületre Azure Resource Manager sablon használatával](essentials/quick-collect-activity-log-arm.md) – új cikk.

Új és frissített cikkek a platform log-tartalmak újrastrukturálása és összevonása terén

- [Azure-beli erőforrás-naplók archiválása a Storage-fiókba](./essentials/resource-logs.md#send-to-azure-storage)
- [Azure Activity log esemény sémája](essentials/activity-log-schema.md)
- [Azure-tevékenység naplója](essentials/activity-log.md)
- [CLI-minták Azure Monitor](/cli-samples.md)
- [PowerShell-minták Azure Monitor](/powershell-samples.md)
- [Útmutató az Azure Monitor REST API-hoz](essentials/rest-api-walkthrough.md)
- [Az Azure Resource naplózza a támogatott szolgáltatásokat és sémákat](./essentials/resource-logs-schema.md)
- [Azure-erőforrásnaplók](essentials/resource-logs.md)
- [Azure-beli tevékenységek naplójának összegyűjtése és elemzése Azure Monitor](./essentials/activity-log.md)
- [Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Diagnosztikai beállítások létrehozása a platformnaplók és -metrikák más célhelyekre való küldéséhez](essentials/diagnostic-settings.md)
- [Az Azure-tevékenység naplójának exportálása](./essentials/activity-log.md#legacy-collection-methods)
- [Az Azure-platform naplóinak áttekintése](essentials/platform-logs-overview.md)
- [Azure platform-naplók továbbítása egy Event hubhoz](./essentials/resource-logs.md#send-to-azure-event-hubs)
- [Azure-Tevékenységnaplók eseményeinek megtekintése Azure Monitor](./essentials/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
- Az Azure arc befoglalásához [engedélyezze a virtuálisgép-bepillantást a Azure Portal](./vm/vminsights-enable-portal.md) -frissítve.
- A [VM-információk áttekintésének engedélyezése](vm/vminsights-enable-overview.md) – frissítve az Azure arc befoglalására.
- [Mi az a VM-alapú adatfelismerés?](vm/vminsights-overview.md) -Frissítve az Azure arc befoglalásával.


### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzetek adatforrásai](visualize/workbooks-data-sources.md) – a riasztások és az egyéni végpontok szakasz.
- [Hibaelhárítás Azure monitor munkafüzet-alapú bepillantást](insights/troubleshoot-workbooks.md) – új cikk.
- [Log Analytics irányítópult-vizualizációk frissítése](logs/dashboard-upgrade.md) – új cikk.



## <a name="may-2020"></a>2020. május

### <a name="general"></a>Általános kérdések

- [Azure monitor GYIK](faq.md) – további szakasz a mérőszámokhoz.
- [Azure monitor ügyfél által felügyelt kulcs](logs/customer-managed-keys.md) – az általános elérhetőségre való felkészülés különböző változásai.
- [Beépített szabályzat-definíciók a Azure monitor](./policy-reference.md) új cikkhez.
- [Felhasználói tulajdonban lévő Storage-fiókok a naplók](logs/private-storage.md) betöltéséhez – új cikk.
- [A Azure monitor-naplók használatának és költségeinek kezelése – a](logs/manage-cost-storage.md) fürt arányos számlázással bővült.
- Az [Azure Private link használatával biztonságosan csatlakoztathatja a hálózatokat Azure monitor](logs/private-link-security.md) új cikkhez.


#### <a name="new-resource-manager-template-samples"></a>Új Resource Manager-sablonok – minták 
- [Resource Manager-sablonok – minták a Azure Monitor](/resource-manager-samples.md)
- [Resource Manager-sablonok – minták a műveleti csoportokhoz](alerts/resource-manager-action-groups.md)
- [Resource Manager-sablonok – minták az ügynökökhöz](agents/resource-manager-agent.md)
- [Resource Manager-sablonok minták a tárolók bepillantást](containers/resource-manager-container-insights.md)
- [Resource Manager-sablonok – minták a virtuális gépekhez](vm/resource-manager-vminsights.md)
- [Resource Manager-sablonok – minták a diagnosztikai beállításokhoz](essentials/resource-manager-diagnostic-settings.md)
- [Resource Manager-sablonok – minták Log Analytics munkaterületekhez](logs/resource-manager-workspace.md)
- [Resource Manager-sablonok – minták a naplók lekérdezéséhez](logs/resource-manager-log-queries.md)
- [Resource Manager-sablonok – minták a naplózási lekérdezés riasztási szabályaihoz](alerts/resource-manager-alerts-log.md)
- [Resource Manager-sablonok – minták metrikus riasztási szabályokhoz](alerts/resource-manager-alerts-metric.md)
- [Resource Manager-sablonok – minták a munkafüzetek számára](visualize/resource-manager-workbooks.md)

### <a name="agents"></a>Ügynökök
- [Telepítse és konfigurálja a Windows Azure Diagnostics bővítményt (wad)](agents/diagnostics-extension-windows-install.md) – további részletek a diagnosztika konfigurálásáról.
- [Log Analytics Agent áttekintése](agents/log-analytics-agent.md) – támogatott Linux-verziók hozzáadása.

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
- [alkalmazás () kifejezés Azure Monitor log lekérdezésekben](logs/app-expression.md)
- [A lekérdezés hatókörének naplózása Azure Monitor Log Analytics](logs/scope.md)
- [Erőforrások lekérdezése Azure Monitor](logs/cross-workspace-query.md)
- [Azure Monitor naplóbejegyzések szabványos tulajdonságai](./logs/log-standard-columns.md)
- [Azure Monitor naplók szerkezete](./logs/data-platform-logs.md)





### <a name="containers"></a>Tárolók
- [A tárolók beszerzésének engedélyezése – a](containers/container-insights-onboard.md) tűzfal konfigurációs táblázatának frissítése.
- A [mérőszámok tároló](containers/container-insights-update-metrics.md) -információinak frissítése – frissítés a felügyelt identitások használatára a metrikák összegyűjtéséhez.
- [A Container-eredmények figyelési díja](containers/container-insights-cost.md) – új cikk.
- [Tároló-elemzések élő adatának beállítása (előzetes verzió)](containers/container-insights-livedata-setup.md) – az új fürt szerepkör-kötés támogatása.

### <a name="insights"></a>Insights
- [Azure monitor az Azure cache for Redis (előzetes verzió)](insights/redis-cache-insights-overview.md) – új cikk.
- [Key Vault figyelése Azure monitor for Key Vault (előzetes verzió)](./insights/key-vault-insights-overview.md) – új cikk.

### <a name="logs"></a>Naplók
- [Hozzon létre & konfigurálja a log Analyticst a PowerShell](logs/powershell-workspace-configuration.md) által hozzáadott hibaelhárítási szakaszban.
- [Hozzon létre egy log Analytics munkaterületet a Azure Portal](logs/quick-create-workspace.md) által felvett hibaelhárítás szakaszban.
- [Log Analytics munkaterület létrehozása az Azure CLI](logs/quick-create-workspace-cli.md) -vel – új hibaelhárítási szakasz.
- Az [Azure log Analytics munkaterület törlése és helyreállítása](logs/delete-workspace.md) – frissített információk a törölt munkaterület helyreállításáról.
- [Függvények Azure monitor log lekérdezésekben](logs/functions.md) – eltávolított Megjegyzés a függvények nem tartalmazó függvényekről.
- [Azure monitor naplók szerkezete – a](./logs/data-platform-logs.md) Application Insights táblázatra vonatkozó egyértelmű tulajdonságok leírása.
- [Használjon Azure monitor naplókat a Azure Logic apps és a Power Automate](logs/logicapp-flow-connector.md) – hozzáadási korlátok szakaszban.
- [A PowerShell használatával hozzon létre és konfiguráljon egy log Analytics munkaterület](logs/powershell-workspace-configuration.md) által hozzáadott hibaelhárítás szakaszt.


### <a name="metrics"></a>Mérőszámok
- [Azure monitor támogatott mérőszámok erőforrástípus szerint](essentials/metrics-supported.md) – egyértelmű vendég metrikák és metrikák útválasztása. 

### <a name="solutions"></a>Megoldások
- [Optimalizálja Active Directory-környezetét Azure monitor](insights/ad-assessment.md) által támogatott verziókhoz hozzáadott Windows Server 2019-es verzióval.
- [Optimalizálja SQL Server-környezetét a Azure monitor](insights/sql-assessment.md) által támogatott SQL Server-verziókhoz.


### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
- A [VM-információk áttekintésének engedélyezése](vm/vminsights-enable-overview.md) – az Ubuntu Server támogatott verzióihoz lett hozzáadva. Log Analytics munkaterület által támogatott régiók lettek hozzáadva.
- A [teljesítmény és a virtuális](vm/vminsights-performance.md) gépek megállapítása – a korlátozásokkal kapcsolatos korlátozások szakasz a nem elérhető metrikák esetében.

### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzetek és Azure Resource Manager sablonok](visualize/workbooks-automate.md) – a Resource Manager-frissítés hozzáadva egy munkafüzet-sablon telepítéséhez.
- [Azure monitor munkafüzetek csoportjai](./visualize/workbooks-groups.md) – új cikk.
- [Azure monitor munkafüzetek – JSON-adatátalakítás JSONPath](visualize/workbooks-jsonpath.md) – új cikk.


## <a name="april-2020"></a>2020. április

### <a name="general"></a>Általános kérdések

- A [Azure monitor ügyfél által felügyelt kulcshoz](logs/customer-managed-keys.md) hozzáadott szakasz az aszinkron műveletekhez
- [Log Analytics munkaterületek kezelése Azure monitor](logs/manage-access.md) – frissített egyéni naplók szakaszban.

### <a name="alerts"></a>Riasztások

- [Azure monitor riasztásokra vonatkozó műveleti szabályok](alerts/alerts-action-rules.md) – videó hozzáadva.
- [A riasztások és az értesítések monitorozásának áttekintése az Azure-ban](alerts/alerts-overview.md) – videó hozzáadva.

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

- [Az Azure Red Hat OpenShift v4. x konfigurálása a Container bepillantásokkal](containers/container-insights-azure-redhat4-setup.md) – új cikk.
- [ServiceNow-szinkronizálási problémák manuális javítása](alerts/itsmc-resync-servicenow.md) – új cikk.
- [Az Azure és a Red Hat OpenShift v4-fürt figyelésének leállítása](containers/container-insights-optout-openshift-v4.md) – új cikk.
- [Az Azure Red Hat OpenShift v3-fürt figyelésének leállítása](containers/container-insights-optout-openshift-v3.md) – új cikk.
- [A hibrid Kubernetes-fürt figyelésének leállítása](containers/container-insights-optout-hybrid.md) – új cikk.

### <a name="insights"></a>Insights

- [Azure Key vaultok figyelése Azure monitor a Key vaultok számára (előzetes verzió)](insights/key-vault-insights-overview.md) – új cikk.

### <a name="logs"></a>Naplók

- [Azure monitor szolgáltatás korlátai](service-limits.md) – a felhasználói lekérdezések szabályozása.
- [Azure monitor naplók használatának és költségeinek kezelése](logs/manage-cost-storage.md) – a naplókhoz hozzáadott számlázás. Kusto-lekérdezés hozzáadva, amely lehetővé teszi az ügyfelek számára a csomópontok közötti örökölt díjszabási szintet annak megállapítására, hogy a felhasználóknak GB vagy Capacity foglalási szinten kell áttérniük.

### <a name="metrics"></a>Mérőszámok

- [Az Azure Metrikaböngésző](essentials/metrics-charts.md) által hozzáadott összesítési szakasz speciális funkciói.

### <a name="workbooks"></a>Munkafüzetek

- [Azure monitor munkafüzetek és Azure Resource Manager sablonok](visualize/workbooks-automate.md) – a munkafüzet-sablonok üzembe helyezéséhez hozzáadott Resource Manager-sablon.

## <a name="march-2020"></a>2020. március

### <a name="general"></a>Általános kérdések

- [Azure monitor áttekintése](overview.md) – Azure monitor áttekintő videó hozzáadva.
- [Azure monitor ügyfél által felügyelt kulcs konfigurációjának](logs/customer-managed-keys.md) általános frissítései.
- [Azure monitor adathivatkozás](/azure/azure-monitor/reference/) – új webhely.

### <a name="alerts"></a>Riasztások

- [Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure monitor – a](alerts/alerts-activity-log.md) Resource Manager-sablonok további magyarázata.
- [Ismerje meg, hogyan működnek a metrikus riasztások Azure Monitorban.](alerts/alerts-metric-overview.md) -Frissítve a kormányzati támogatáshoz.
- [Hibaelhárítás Azure monitor riasztások és értesítések](alerts/alerts-troubleshoot.md) – új cikk.

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights automatizálása a PowerShell](app/powershell.md) által hozzáadott ARMClient-példákkal.
- [Telemetria folyamatos exportálása a Application Insightsból](app/export-telemetry.md) – tábla hozzáadása az exportálási struktúra részleteivel.
- [Snapshot Debugger engedélyezése a .NET-alkalmazásokhoz Azure app Service](app/snapshot-debugger-appservice.md) – hozzáadott Resource Manager-sablon – példa.
- [Az Azure Application Insights-használat és-költségek kezelése](app/pricing.md) az adatkorláttal kapcsolatos riasztásokkal kapcsolatban.
- [Python-alkalmazások figyelése Azure monitor (előzetes verzió)](app/opencensus-python.md) – szabványos metrikák hozzáadása.
- [Forrás-hozzárendelési támogatás JavaScript-alkalmazásokhoz – Azure Monitor Application Insights](app/source-map-support.md) – új cikk.

### <a name="containers"></a>Tárolók

- [Azure monitor GYIK](faq.md) – a tároló-felismerések frissítése.
- [A GPU-figyelés konfigurálása a Container bepillantásokkal](containers/container-insights-gpu-monitoring.md) – új cikk.

### <a name="insights"></a>Insights

- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – frissített elavulás dátuma.

### <a name="logs"></a>Naplók

- A Azure Monitor által hozzáadott CPU-feltételhez tartozó [napló-lekérdezések optimalizálása](logs/query-optimization.md) az XML-és JSON-elemzésekhez.
- [Az Azure log Analytics munkaterület törlése és helyreállítása](logs/delete-workspace.md) – hibaelhárítás.
- [Azure monitor naplók használata Azure Logic apps és energiagazdálkodási automatizálással](logs/logicapp-flow-connector.md) – frissítve az új Azure monitor-összekötőhöz.

### <a name="metrics"></a>Mérőszámok

- [A lemez metrikáinak elavulása a Azure Portal](essentials/portal-disk-metrics-deprecation.md) új cikkben.
- [Oktatóanyag – mérőszámok diagramjának létrehozása Azure monitor](essentials/tutorial-metrics-explorer.md) által hozzáadott videóban.

### <a name="platform-logs"></a>Platformnaplók

- [Összegyűjtheti és elemezheti az Azure-beli tevékenység naplóját Azure monitor](./essentials/activity-log.md) – újraírhatja, hogy jobban megmagyarázza a begyűjtési tevékenység naplójának diagnosztikai beállításait.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

- [Azure-beli virtuális gépek figyelése Azure monitor](vm/monitor-vm-azure.md) -új cikkel.
- Gyors útmutató [: Azure-beli virtuális gépek figyelése Azure monitor](vm/quick-monitor-azure-vm.md) -frissítve a VM-ismeretek hozzáadásához.
- [A VM-ismeretekből származó riasztások](vm/vminsights-alerts.md) – új cikk.
- A [VM-információk áttekintésének engedélyezése](vm/vminsights-enable-overview.md) – az ügynök letöltési hivatkozásainak frissítése.

A VM-információk általánosan elérhető általános frissítései

- [Mi az a VM-alapú adatfelismerés?](vm/vminsights-overview.md)
- [A VM-ismeretek (GA) gyakori kérdései](vm/vminsights-ga-release-faq.md) 
- [A virtuális gépek bepillantást tesznek a Azure Policy használatával](./vm/vminsights-enable-policy.md) 
- [A teljesítmény diagramon való elkészítése a virtuális gépekkel](vm/vminsights-performance.md)
- [Naplók lekérdezése a VM-elemzésből](vm/vminsights-log-search.md)
- [Alkalmazás-függőségek megtekintése a VM-alapú ismeretekkel](vm/vminsights-maps.md) 

### <a name="visualizations"></a>Vizualizációk

- [Adatok megjelenítése Azure monitorról](visualizations.md) – frissítve a View Designer tervezett elavulása megjegyzéssel.

## <a name="february-2020"></a>2020. február

### <a name="agents"></a>Ügynökök

Több frissítés a diagnosztikai bővítmény tartalmának újraírása részeként.

- [Az Azure monitoring-ügynökök áttekintése](agents/agents-overview.md) – átstrukturált táblázatok az egyes ügynökök egyedi funkcióinak jobb tisztázásához.
- [Azure Diagnostics bővítmény áttekintése](agents/diagnostics-extension-overview.md) – teljes újraírás.
- A [blob Storage használata az IIS-hez és a Table Storage](essentials/diagnostics-extension-logs.md) -hoz a frissítéshez és a tisztasághoz Azure monitor általános újraírással kapcsolatos eseményekhez.
- [Telepítse és konfigurálja a Windows Azure Diagnostics bővítményt (wad)](agents/diagnostics-extension-windows-install.md) – új cikk. 
- [Windows Diagnostics bővítmény sémája](agents/diagnostics-extension-schema-windows.md) – átrendezve.
- [Adatok küldése a Windows Azure Diagnostics bővítményből az azure Event Hubsba](agents/diagnostics-extension-stream-event-hubs.md) – teljes mértékben újraírható és frissítve.
- [Diagnosztikai adatgyűjtés tárolása és megtekintése az Azure Storage-ban](../cloud-services/diagnostics-extension-to-storage.md) – teljes mértékben újraírható és frissíthető.
- [Log Analytics virtuálisgép-bővítmény a Windowshoz](../virtual-machines/extensions/oms-windows.md) – jobban tisztázza log Analytics ügynökkel való kapcsolatot.
- [Azure monitor virtuálisgép-bővítmény a Linux](../virtual-machines/extensions/oms-linux.md) rendszerhez – jobban tisztázza log Analytics-ügynökkel való kapcsolatot.

### <a name="application-insights"></a>Application Insights

- [A kapcsolatok karakterláncai az Azure Application Insightsban](app/sdk-connection-string.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat

#### <a name="container-insights"></a>Tároló-felismerések

- [Azure Active Directory integrálása az Azure Kubernetes szolgáltatással](../aks/azure-ad-integration-cli.md) – vegye figyelembe, hogy egy ügyfélalkalmazás létrehozásával támogatja a Kubernetes RBAC-kompatibilis fürtöt a tároló-felismerések támogatásához.

#### <a name="vm-insights"></a>VM-ismeretek

- Virtuálisgép-elemzések [(GA) – gyakori kérdések](vm/vminsights-ga-release-faq.md) – a teljesítményadatok tárolási módjának módosítása.

#### <a name="office-365"></a>Office 365

- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – frissített elavulás dátuma.


### <a name="logs"></a>Naplók

- Azure Monitor-új cikkben [található naplók optimalizálása](logs/query-optimization.md) .
- [Azure monitor naplók használatának és költségeinek kezelése](logs/manage-cost-storage.md) – továbbfejlesztett minta-lekérdezések, amelyek segítenek a használat megértésében.

### <a name="metrics"></a>Mérőszámok

- [Azure monitor a platform metrikái exportálhatók a diagnosztikai beállítások](essentials/metrics-supported-export-diagnostic-settings.md) – hozzáadott szakasz a nullák és a nulla értékek viselkedésének változása esetén.

### <a name="visualizations"></a>Vizualizációk

Több új cikk a Designer for munkafüzetek átalakítási útmutatójában.

- [Azure monitor a Designer és a munkafüzetek közötti áttérési útmutató](visualize/view-designer-conversion-overview.md) – új cikk.
- [Azure monitor tervező átalakítása munkafüzetekbe konvertálási beállítások](visualize/view-designer-conversion-options.md) – új cikk.
- [Azure monitor megtekintheti a tervezőt munkafüzetekhez csempe-konverziók](visualize/view-designer-conversion-tiles.md) – új cikk.
- [Azure monitor megtekintheti a tervezőt a munkafüzetek átalakításának összegzése és a hozzáférés](visualize/view-designer-conversion-access.md) – új cikk alapján.
- [Azure monitor tervező átalakítása munkafüzetekhez gyakori feladatok konvertálása](visualize/view-designer-conversion-tasks.md) – új cikk.
- [Azure monitor tervező átalakítása munkafüzetek átalakítására – példák](visualize/view-designer-conversion-examples.md) – új cikk.

## <a name="january-2020"></a>2020. január

### <a name="general"></a>Általános kérdések

- [Mi figyeli a Azure Monitor?](monitor-reference.md) – Új cikk.

### <a name="agents"></a>Ügynökök

- [A naplófájlok adatainak összegyűjtése az Azure log Analytics agenttel – a](agents/log-analytics-agent.md) hálózati tűzfalra vonatkozó követelmények tábla frissítése.

### <a name="alerts"></a>Riasztások

- [Műveleti csoportok létrehozása és kezelése a Azure Portalban](alerts/action-groups.md) – a már nem szükséges v2-függvények eltávolításának beállítása.
- [Metrikai riasztás létrehozása Resource Manager-sablonnal](alerts/alerts-metric-create-templates.md) – példa a *ignoreDataBefore* paraméterhez.  A többszörös feltételi szabályokkal kapcsolatos korlátozásokat adott meg.
- [Log Analytics riasztás használata REST API](alerts/api-alerts.md) – JSON-példa kijavítva.

### <a name="application-insights"></a>Application Insights

- Az [Application Insights által használt IP-címek és a log Analytics](app/ip-addresses.md) – a rendelkezésre állási teszt szakasz frissítése a bejövő portszabály hozzáadásával az Azure hálózati biztonsági csoportokat használó forgalom engedélyezéséhez.
- [Az Azure Application Insights Profiler kapcsolatos problémák elhárítása](app/profiler-troubleshooting.md) – frissített általános hibaelhárítás.
- [Telemetria mintavételezés az Azure Application Insightsban](app/sampling.md) – frissített és átstrukturált, hogy javítsa az olvashatóságot az ügyfelek visszajelzései alapján.

### <a name="data-security"></a>Adatbiztonság

- [Azure monitor ügyfél által felügyelt kulcs konfigurálása](logs/customer-managed-keys.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat

#### <a name="container-insights"></a>Tároló-felismerések

- A [tároló-elemzések ügynöke adatgyűjtésének konfigurálása](containers/container-insights-agent-config.md) – további információk az ügynök frissítéséhez az Azure Red Hat OpenShift, és további információkkal bővült az ügynök verziófrissítésének módszerei.
- [Teljesítmény-riasztások létrehozása a tárolók](./containers/container-insights-log-alerts.md) elemzéséhez – felülvizsgált információk és a munkaterületen tárolt teljesítményadatok riasztások létrehozására vonatkozó frissített lépések a munkaterület-riasztások használatával.
- [Kubernetes-figyelés a Container](containers/container-insights-analyze.md) -elemzésekkel – a Windows Kubernetes-fürtök támogatásáról szóló áttekintő cikk és a elemzésre vonatkozó cikk frissítése.
- [Azure Red Hat OpenShift-fürtök konfigurálása a Container bepillantásokkal](containers/container-insights-azure-redhat-setup.md) – további részletek az ügynök frissítéséhez az Azure Red Hat OpenShift, és további információkkal bővült az ügynök verziófrissítésének módszerei.
- A [hibrid Kubernetes-fürtöket a tároló-bepillantásokkal konfigurálhatja](containers/container-insights-hybrid-setup.md) – frissítve a biztonságos port hozzáadásának támogatásával: a 10250 a Kubelet cAdvisor.
- [A Container-elemzések ügynökének kezelése](containers/container-insights-manage-agent.md) – az Azure Red Hat OpenShift származó, a metrikák és a konfigurációk, valamint az egyéb típusú Kubernetes-fürtök összevetésével kapcsolatos részletes részletek.
- A [Container bepillantást a Prometheus-integrációra](containers/container-insights-prometheus-integration.md) – a metrikus adatoknak az Azure Red Hat-OpenShift való működésével és konfigurációval kapcsolatos frissített részletei a más típusú Kubernetes-fürtökhöz képest.
- A [metrikák tároló](containers/container-insights-update-metrics.md) -elemzésének frissítése – az Azure Red Hat OpenShift származó, a metrikák és a konfiguráció adatainak a Kubernetes-fürtökhöz képest történő frissítésével kapcsolatos részletes részletek.

#### <a name="vm-insights"></a>VM-ismeretek

- [VM-információk (GA) – gyakori kérdések](vm/vminsights-ga-release-faq.md) – további információ a munkaterület és az ügynökök új verzióra való frissítéséről.

#### <a name="office-365"></a>Office 365

- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – további részletek és gyakori kérdések az Office 365-megoldás Azure sentinelben való áttelepítéséről. A bevezetési szakasz el lett távolítva.

### <a name="logs"></a>Naplók

- [Log Analytics munkaterületek kezelése Azure monitor](logs/manage-access.md) – nem műveletekre vonatkozó frissítések.
- [Azure monitor naplók használatának és költségeinek kezelése](logs/manage-cost-storage.md) – az adatmennyiség kiszámításának pontosítása a díjszabási modell szakaszban.
- [Azure Resource Manager-sablonokkal létrehozhat és konfigurálhat egy log Analytics-munkaterületet](./logs/resource-manager-workspace.md) frissített sablont új díjszabási szintek használatával.

### <a name="platform-logs"></a>Platformnaplók

- [Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal – Azure monitor](./essentials/activity-log.md) – további információ a módosult tulajdonságokkal kapcsolatban.
- [Exportálja az Azure-tevékenység naplóját](./essentials/activity-log.md#legacy-collection-methods) – frissítve a felhasználói felület változásaihoz. 

## <a name="december-2019"></a>2019. december

### <a name="agents"></a>Ügynökök

- [Linux rendszerű számítógépek Összekapcsolásának Azure monitor](agents/agent-linux.md) – új cikk.

### <a name="alerts"></a>Riasztások

- [Metrikus riasztás létrehozása Resource Manager-sablonnal](alerts/alerts-metric-create-templates.md) – példa az egyéni metrikára.
- Dinamikus [küszöbértékekkel rendelkező riasztások létrehozása Azure monitor](alerts/alerts-dynamic-thresholds.md) – hozzáadott szakasz a dinamikus küszöbértékű diagramok értelmezéséhez.
- [A riasztások és az értesítések monitorozásának áttekintése az Azure-ban](alerts/alerts-overview.md) – frissített Resource Graph-lekérdezés.
- A [metrikus riasztások támogatott erőforrásai Azure monitor](alerts/alerts-metric-near-real-time.md) – a metrikák és a dimenziók frissítése támogatott.
- [Váltás az örökölt log Analytics riasztások API-ból az új Azure-riasztások API-ba](alerts/alerts-log-api-switch.md) – Megjegyzés hozzáadva a módosított riasztás nevéhez.
- [Ismerje meg, hogyan működnek a metrikus riasztások Azure Monitorban.](alerts/alerts-metric-overview.md) – Támogatott erőforrástípusok hozzáadása a nagy léptékű figyeléshez.

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

- A Container-elemzések [gyakori kérdései](./faq.md) – további kérdés a rendszerkép és a név mezőkben.
- [Azure SQL Analytics megoldás a Azure monitor](insights/azure-sql.md) -frissítve adatbázisban vár felügyelt példányok támogatása.
- A [tároló-elemzések ügynökének adatgyűjtési](containers/container-insights-agent-config.md) beállítása – a enrich_container_logshoz hozzáadott beállítás.
- [Hibrid Kubernetes-fürtök konfigurálása a Container bepillantások szolgáltatással](containers/container-insights-hybrid-setup.md) – új hibaelhárítási szakasz.
- [Active Directory replikáció állapotának figyelése Azure monitor](insights/ad-replication-status.md) -.NET-keretrendszerre vonatkozó előfeltétel-frissítéssel.
- [Network Performance monitor megoldás az Azure-ban](insights/network-performance-monitor.md) – támogatott régiók.
- [A Active Directory-környezet optimalizálása Azure monitor](insights/ad-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [A SQL Server-környezet optimalizálása Azure monitor](insights/sql-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [Optimalizálja System Center Operations Manager környezetét az Azure log Analytics](insights/scom-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [Támogatott kapcsolatok az Azure Log Analytics it-szolgáltatásmenedzsmenti csatolóával](alerts/itsmc-connections.md) – új York-i előfeltétel-ügyfél-azonosító és az ügyfél titkos kulcsa.

### <a name="logs"></a>Naplók

- [Az Azure log Analytics munkaterület törlése és helyreállítása](logs/delete-workspace.md) – PowerShell-metódus hozzáadva.
- [Megtervezheti a Azure monitor naplók üzembe helyezését](logs/design-logs-deployment.md) – a munkaterületek terhelési arányának növelését.

### <a name="metrics"></a>Mérőszámok

- [Azure monitor a platform metrikái exportálhatók a diagnosztikai beállítások használatával](essentials/metrics-supported-export-diagnostic-settings.md) – új cikk.

### <a name="platform-logs"></a>Platformnaplók

Több cikk is frissült a tartalom átszervezésének részeként a platform naplóiban a diagnosztikai beállítások használatával a műveletnapló konfigurálására szolgáló új funkció alapján.

- [Azure-beli erőforrás-naplók archiválása a Storage-fiókba](./essentials/resource-logs.md#send-to-azure-storage)
- [Azure Activity log esemény sémája](essentials/activity-log-schema.md)
- [Azure Monitor szolgáltatási korlátok](service-limits.md)
- [Azure-beli tevékenység-naplók összegyűjtése és elemzése Log Analytics munkaterületen](./essentials/activity-log.md)
- [Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal (előzetes verzió) – Azure Monitor](./essentials/activity-log.md)
- [Azure-beli tevékenységek naplóinak begyűjtése egy Log Analytics munkaterületre az Azure-bérlők között](./essentials/activity-log.md)
- [Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Diagnosztikai beállítás létrehozása az Azure-ban Resource Manager-sablon használatával](./essentials/resource-manager-diagnostic-settings.md)
- [Diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](essentials/diagnostic-settings.md)
- [Az Azure-tevékenység naplójának exportálása](./essentials/activity-log.md#legacy-collection-methods)
- [Az Azure-platform naplóinak áttekintése](essentials/platform-logs-overview.md)
- [Azure monitoring-adatstreamek továbbítása az Event hub szolgáltatásba](essentials/stream-monitoring-data-event-hubs.md)
- [Azure platform-naplók továbbítása egy Event hubhoz](./essentials/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Rövid útmutatók és oktatóanyagok

- [Metrikai diagram létrehozása Azure monitor](essentials/tutorial-metrics-explorer.md) – új cikkben.
- [Erőforrás-naplók összegyűjtése Azure-erőforrásokból és Azure monitor](essentials/tutorial-resource-logs.md) -új cikk elemzése.
- [Azure-erőforrás figyelése Azure monitor](essentials/quick-monitor-azure-resource.md) -új cikkel.
   
## <a name="next-steps"></a>Következő lépések

- Ha szeretne hozzájárulni Azure Monitor dokumentációhoz, tekintse meg a [docs közreműködői útmutatóját](/contribute/).