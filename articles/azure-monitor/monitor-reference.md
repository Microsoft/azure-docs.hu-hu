---
title: Mit figyelnek a Azure Monitor
description: A szolgáltatás által figyelt összes szolgáltatás és egyéb erőforrás Azure Monitor.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 513f262f5d09cf56c4506a4f20c9aa41507c2abd
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515277"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Mit figyelnek a Azure Monitor?
Ez a cikk a szolgáltatások által figyelt különböző alkalmazásokat és szolgáltatásokat Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Elemzések és alapvető megoldások
Az alapvető elemzések és megoldások a Azure Monitor részét képezi, és követik az Azure támogatási és szolgáltatói szerződését. Minden olyan Azure-régióban támogatottak, ahol Azure Monitor elérhető.

### <a name="insights"></a>Insights

Az elemzések testreszabott figyelési élményt biztosítanak az egyes alkalmazásokhoz és szolgáltatásokhoz. Naplókat és metrikákat gyűjtenek és elemeznek.

| Elemzések | Leírás |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Extensible Application Performance Management (APM) szolgáltatás az élő webalkalmazás figyelése bármely platformon. |
| [Tárolóelemzések](containers/container-insights-overview.md) | Monitorozza az Azure Container Instances -on (AKS Azure Kubernetes Service üzemeltetett kubernetes-fürtökön üzembe helyezett tárolók számítási feladatainak teljesítményét. |
| [Azure Monitor for Cosmos DB](insights/cosmosdb-insights-overview.md) | Az összes erőforrás általános teljesítményét, meghibásodását, kapacitását és működési állapotát Azure Cosmos DB egységes interaktív felhasználói élményben. |
| [hálózati Azure Monitor (előzetes verzió)](insights/network-insights-overview.md) | Átfogó képet nyújt az összes hálózati erőforrás állapotáról és metrikákról. A speciális keresési funkcióval egyszerűen rákeresve azonosíthatja az erőforrás-függőségeket, és olyan forgatókönyveket engedélyez, mint például a webhelyet üzemeltető erőforrások azonosítása. |
[Azure Monitor (előzetes verzió)](insights/resource-group-insights.md) |  Az egyes erőforrások által tapasztalt problémák osztályozása és diagnosztizálása, miközben kontextust biztosít az erőforráscsoport egészének állapotáról és teljesítményéről. |
| [Azure Monitor Storage-hez](insights/storage-insights-overview.md) | Átfogó monitorozást biztosít Azure Storage-fiókjairól az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes áttekintésével. |
| [Virtuálisgép-elemzések](vm/vminsights-overview.md) | Nagy méretekben monitorozza az Azure-beli virtuális gépeket és virtuálisgép-méretezési készleteket. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. |
| [Azure Monitor (előzetes Key Vault)](./insights/key-vault-insights-overview.md) | Átfogó monitorozást biztosít a kulcstartókról, és egységes nézetet biztosít a Key Vault, teljesítményről, hibákról és késésről. |
| [Azure Monitor (előzetes Azure Cache for Redis)](insights/redis-cache-insights-overview.md) |  Egységes, interaktív nézetet biztosít az általános teljesítményről, a hibákról, a kapacitásról és a működési állapotról. |


### <a name="core-solutions"></a>Alapvető megoldások

A megoldások egy adott alkalmazáshoz vagy szolgáltatáshoz testreszabott naplólekérdezéseken és nézeteken alapulnak. Csak a naplókat gyűjtik össze és elemzik, és az elemzések érdekében idővel elavultak.

| Megoldás | Leírás |
|:---|:---|
| [Ügynök állapota](insights/solution-agenthealth.md) | A Log Analytics-ügynökök állapotának és konfigurációjának elemzése. |
| [Riasztáskezelés](insights/alert-management-solution.md) | Elemezheti a System Center Operations Manager, Nagiosból vagy Zabbixből gyűjtött riasztásokat. |
| [Service Map](vm/service-map.md) | Automatikusan felderíti a Windows- és Linux-rendszerek alkalmazás-összetevőit, és leképezi a szolgáltatások közötti kommunikációt. |



## <a name="azure-services"></a>Azure-szolgáltatások
Az alábbi táblázat az Azure-szolgáltatásokat és az ezekbe gyűjtött adatokat Azure Monitor. 

- Metrikák – A szolgáltatás automatikusan metrikákat gyűjt Azure Monitor metrikákba. 
- Naplók – A szolgáltatás támogatja a diagnosztikai beállításokat, amelyek platformnaplókat és metrikákat gyűjtenek a Azure Monitor naplókhoz.
- Elemzés – A szolgáltatáshoz elérhető egy olyan elemzés, amely testreszabott figyelési élményt biztosít a szolgáltatáshoz.

| Szolgáltatás | Mérőszámok | Naplók | Elemzések | Jegyzetek |
|:---|:---|:---|:---|:---|
|Active Directory | Nem | Igen | [Igen](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Nem | Nem | Nem |  |
|Active Directory Domain Services | Nem | Igen | Nem |  |
|Tevékenységnapló | Nem | Igen | Nem | |
|Advanced Threat Protection | Nem | Nem | Nem |  |
|Advisor | Nem | Nem | Nem |  |
|AI Builder | Nem | Nem | Nem |  |
|Analysis Services | Igen | Igen | Nem |  |
|API for FHIR | Nem | Nem | Nem |  |
|API Management | Igen | Igen | Nem |  |
|App Service | Igen | Igen | Nem |  |
|AppConfig | Nem | Nem | Nem |  |
|Application Gateway | Igen | Igen | Nem |  |
|Igazolási szolgáltatás | Nem | Nem | Nem |  |
|Automation | Igen | Igen | Nem |  |
|Azure Service Manager (RDFE) | Nem | Nem | Nem |  |
|Backup | Nem | Igen | Nem |  |
|Bastion | Nem | Nem | Nem |  |
|Batch | Igen | Igen | Nem |  |
|Batch AI | Nem | Nem | Nem |  |
|Blockchain szolgáltatás | Nem | Igen | Nem |  |
|Blueprints | Nem | Nem | Nem |  |
|Robotszolgáltatás | Nem | Nem | Nem |  |
|Cloud Services | Igen | Igen | Nem | A vendég operációs rendszer és a munkafolyamatok figyelése érdekében szükséges ügynök.  |
|Cloud Shell | Nem | Nem | Nem |  |
|Cognitive Services | Igen | Igen | Nem |  |
|Container Instances | Igen | Nem | Nem |  |
|Container Registry | Igen | Igen | Nem |  |
|Content Delivery Network (CDN) | Nem | Igen | Nem |  |
|Cosmos DB | Igen | Igen | [Igen](insights/cosmosdb-insights-overview.md) |  |
|Költségkezelés | Nem | Nem | Nem |  |
|Data Box | Nem | Nem | Nem |  |
|Data Catalog Gen2 | Nem | Nem | Nem |  |
|Data Explorer | Igen | Igen | Nem |  |
|Data Factory | Igen | Igen | Nem |  |
|Data Factory v2 | Nem | Igen | Nem |  |
|Data Share | Nem | Nem | Nem |  |
|Database for MariaDB | Igen | Igen | Nem |  |
|Database for MySQL | Igen | Igen | Nem |  |
|Database for PostgreSQL | Igen | Igen | Nem |  |
|Database Migration Service | Nem | Nem | Nem |  |
|Databricks | Nem | Igen | Nem |  |
|DDoS Protection | Igen | Igen | Nem |  |
|DevOps | Nem | Nem | Nem |  |
|DNS | Igen | Nem | Nem |  |
|Tartománynevek | Nem | Nem | Nem |  |
|Dps | Nem | Nem | Nem |  |
|Dynamics 365 Customer Engagement | Nem | Nem | Nem |  |
|Dynamics 365 Finance and Operations | Nem | Nem | Nem |  |
|Event Grid | Igen | Nem | Nem |  |
|Event Hubs | Igen | Igen | Nem |  |
|ExpressRoute | Igen | Igen | Nem |  |
|Firewall | Igen | Igen | Nem |  |
|Front Door | Igen | Igen | Nem |  |
|Functions | Igen | Igen | Nem |  |
|HDInsight | Nem | Igen | Nem |  |
|HPC Cache | Nem | Nem | Nem |  |
|Information Protection | Nem | Igen | Nem |  |
|Intune | Nem | Igen | Nem |  |
|IoT Central | Nem | Nem | Nem |  |
|IoT Hub | Igen | Igen | Nem |  |
|Key Vault | Igen | Igen | [Igen](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Service (AKS) | Nem | Nem | [Igen](containers/container-insights-overview.md)  |  |
|Load Balancer | Igen | Nem | Nem |  |
|Logic Apps | Igen | Igen | Nem |  |
|Machine Learning szolgáltatás | Nem | Nem | Nem |  |
|Felügyelt alkalmazások  | Nem | Nem | Nem |  |
|Maps  | Nem | Nem | Nem |  |
|Media Services | Igen | Igen | Nem |  |
|Microsoft Felügyelt asztal | Nem | Nem | Nem |  |
|Microsoft PowerApps | Nem | Nem | Nem |  |
|Microsoft Social Engagement | Nem | Nem | Nem |  |
|Microsoft Stream | Igen | Igen | Nem |  |
|Migrate | Nem | Nem | Nem |  |
|Többtényezős hitelesítés | Nem | Igen | Nem |  |
|Network Watcher | Igen | Igen | Nem |  |
|Notification Hubs | Igen | Nem | Nem |  |
|Open Datasets | Nem | Nem | Nem |  |
|Szabályzat | Nem | Nem | Nem |  |
|Power Automate | Nem | Nem | Nem |  |
|Power BI Embedded | Igen | Igen | Nem |  |
|Private Link | Nem | Nem | Nem |  |
|Project Spool Communication Platform | Nem | Nem | Nem |  |
|Red Hat OpenShift | Nem | Nem | Nem |  |
|Redis Cache | Igen | Igen | [Igen](insights/redis-cache-insights-overview.md) | |
|Resource Graph | Nem | Nem | Nem |  |
|Resource Manager | Nem | Nem | Nem |  |
|Kiskereskedelmi keresés – szerző: Bing | Nem | Nem | Nem |  |
|Keresés | Igen | Igen | Nem |  |
|Service Bus | Igen | Igen | Nem |  |
|Service Fabric | Nem | Igen | Nem | A vendég operációs rendszer és a munkafolyamatok figyelése érdekében szükséges ügynök.  |
|Bejelentkezési portál | Nem | Nem | Nem |  |
|Site Recovery | Nem | Igen | Nem |  |
|Spring Cloud Szolgáltatás | Nem | Nem | Nem |  |
|Azure Synapse Analytics | Igen | Igen | Nem |  |
|SQL Database | Igen | Igen | Nem |  |
|SQL Server Stretch Database | Igen | Igen | Nem |  |
|Verem | Nem | Nem | Nem |  |
|Tárolás | Igen | Nem | [Igen](insights/storage-insights-overview.md) |  |
|Tárológyorsítótár | Nem | Nem | Nem |  |
|Társzinkronizálási szolgáltatások | Nem | Nem | Nem |  |
|Stream Analytics | Igen | Igen | Nem |  |
|Time Series Insights | Igen | Igen | Nem |  |
|Tina | Nem | Nem | Nem |  |
|Traffic Manager | Igen | Igen | Nem |  |
|Univerzális nyomtatás | Nem | Nem | Nem |  |
|Virtual Machine Scale Sets | Nem | Igen | [Igen](vm/vminsights-overview.md) | A vendég operációs rendszer és a munkafolyamatok figyelése érdekében szükséges ügynök. |
|Virtual Machines | Igen | Igen | [Igen](vm/vminsights-overview.md) | A vendég operációs rendszer és a munkafolyamatok figyelése érdekében szükséges ügynök. |
|Virtual Network | Igen | Igen | [Igen](insights/network-insights-overview.md) |  |
|Virtual Network – NSG-forgalom naplói | Nem | Igen | Nem |  |
|VPN Gateway | Igen | Igen | Nem |  |
|Windows virtuális asztal | Nem | Igen | Nem |  |

## <a name="virtual-machine-agents"></a>Virtuális gépek ügynökei
Az alábbi táblázat azokat az ügynököket sorolja fel, amelyek adatokat gyűjtenek a virtuális gépek vendég operációs rendszerből, és adatokat küldenek a Monitornak. Minden ügynök különböző adatokat gyűjthet, és elküldheti őket a metrikáknak vagy a naplóknak a Azure Monitor. 

Az [egyes ügynökök által Azure Monitor adatokkal](agents/agents-overview.md) kapcsolatos részletekért lásd a Azure Monitor áttekintését.

| Ügynök |  Mérőszámok | Naplók |
|:---|:---|:---|:---|
| [Azure Monitor ügynök (előzetes verzió)](agents/azure-monitor-agent-overview.md) | Igen | Igen |
| [Log Analytics-ügynök](agents/log-analytics-agent.md) | Nem | Igen|
| [Diagnosztikai bővítmény](agents/diagnostics-extension-overview.md) | Igen | Nem |
| [Telegraf-ügynök](essentials/collect-custom-metrics-linux-telegraf.md) | Igen | Nem |
| [Függőségi ügynök](vm/vminsights-enable-overview.md) | Nem | Igen |


## <a name="product-integrations"></a>Termékintegrációk
Az alábbi táblázatban található szolgáltatások és megoldások egy Log Analytics-munkaterületen tárolják az adataikat, hogy elemezhetők legyen a felhasználók által gyűjtött Azure Monitor.

| Termék/szolgáltatás | Leírás |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Kezelheti az operációs rendszer frissítéseit, és nyomon követheti a módosításokat Windows és Linux rendszerű számítógépeken. Lásd: [Change Tracking](../automation/change-tracking/overview.md) és [Update Management.](../automation/update-management/overview.md) |
| [Azure Information Protection ](/azure/information-protection/) | Dokumentumok és e-mailek besorolása és opcionális védelme. Lásd: [Központi jelentéskészítés a Azure Information Protection.](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports) |
| [Azure Security Center](../security-center/index.yml) | Biztonsági események gyűjtése és elemzése, valamint fenyegetéselemzés végrehajtása. Lásd: [Adatgyűjtés a Azure Security Center](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Különböző forrásokhoz csatlakozik, például az Office 365-höz és Amazon Web Services Cloud Trailhoz. Lásd: [Adatforrások csatlakoztatása.](../sentinel/connect-data-sources.md) |
| [Microsoft Intune](/intune/) | Diagnosztikai beállítás létrehozása a naplóknak a Azure Monitor. Lásd: [Naplóadatok küldése tárolóba, eseményközpontba vagy naplóelemzésbe az Intune-ban (előzetes verzió)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Network (Hálózat)  | [Network Performance Monitor](insights/network-performance-monitor.md) – A szolgáltatás és az alkalmazásvégpont hálózati kapcsolatának és teljesítményének figyelése.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics) – Naplók és metrikák elemzése a Azure Application Gateway.<br>[Traffic Analytics](../network-watcher/traffic-analytics.md) – Elemzi Network Watcher hálózati biztonsági csoport (NSG) forgalmi naplóit, hogy betekintést nyújtson az Azure-felhő adatforgalmába. |
| [Office 365](insights/solution-office-365.md) | Az Office 365-környezet figyelése. Frissített verzió, továbbfejlesztett, a Azure Sentinel. |
| [SQL Analytics](insights/azure-sql.md) | Az adatbázis-adatbázisok Azure SQL SQL Managed Instances teljesítményének nagy léptékű és több előfizetésre való figyelése. |
| [Surface Hub](insights/surface-hubs.md) | Nyomon követheti a különböző eszközök Surface Hub használatát. |
| [System Center Operations Manager](/system-center/scom) | Gyűjtsön adatokat Operations Manager ügynökökről úgy, hogy csatlakoztatja a felügyeleti csoportot a Azure Monitor. Lásd: [Connect Operations Manager to Azure Monitor](agents/om-agents.md)<br> Mérje fel a felügyeleti csoport System Center Operations Manager állapotát a [Operations Manager felmérési megoldással.](insights/scom-assessment.md) |
| [Microsoft Teams-helyiségek](/microsoftteams/room-systems/azure-monitor-deploy) | A Microsoft Teams-helyiségek integrált, teljes mértékben integrált kezelése. |
| [Visual Studio App Center](/appcenter/) | Alkalmazások összeállítása, tesztelése és terjesztése, majd állapotuk és használatuk figyelése. Lásd: [Start analyzing your mobile app with App Center and Application Insights](app/mobile-center-quickstart.md). |
| Windows | [Windows Update megfelelőség](/windows/deployment/update/update-compliance-get-started) – A Windows asztali frissítésének értékelése.<br>[Asztali elemzés –](/configmgr/desktop-analytics/overview) A Konfigurációkezelő integrálva betekintést és intelligenciát biztosít, így megalapozottabb döntéseket hozhat a Windows-ügyfelek frissítésre való alkalmasságával kapcsolatban. |



## <a name="other-solutions"></a>Egyéb megoldások
Más megoldások is elérhetők a különböző alkalmazások és szolgáltatások monitorozására, de az aktív fejlesztés leállt, és előfordulhat, hogy nem minden régióban érhetők el. Ezekre az Azure Log Analytics adatbeszerkesztés szolgáltatásiszint-szerződése vonatkozik.

| Megoldás | Leírás |
|:---|:---|
| [Active Directory állapot-ellenőrzés](insights/ad-assessment.md) | Mérje fel a biztonsági környezetek Active Directory állapotát. |
| [Active Directory replikáció állapota](insights/ad-replication-status.md) | Rendszeresen figyeli a Active Directory a replikációs hibákat. |
| [Tevékenységnapló elemzése](essentials/activity-log.md#activity-log-analytics-monitoring-solution) | Tevékenységnapló-bejegyzések megtekintése. |
| [DNS Analytics (előzetes verzió)](insights/dns-analytics.md) | Gyűjti, elemzi és korrelálja a Windows DNS elemző- és auditnaplóit, valamint a DNS-kiszolgálókról származó egyéb kapcsolódó adatokat. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Gyűjtse össze, tekintse meg és elemezze Cloud Foundry rendszer állapotára és teljesítményére vonatkozó metrikákat több üzemelő példányban. |
| [Containers](containers/containers.md) | Docker- és Windows-tároló gazdagépek megtekintése és kezelése. |
| [Igény szerinti felmérések](/services-hub/health/getting_started_with_on_demand_assessments) | A helyszíni, hibrid és felhőbeli Microsoft-technológiai környezetek rendelkezésre állásának, biztonságának és teljesítményének felmérése és optimalizálása. |
| [SQL-állapot-ellenőrzés](insights/sql-assessment.md) | Mérje fel a biztonsági környezetek SQL Server állapotát.  |
| [Átviteli adatok](insights/wire-data.md) | A Log Analytics-ügynökkel Windows- és Linux-csatlakoztatott számítógépekről gyűjtött összevont hálózati és teljesítményadatok. |

## <a name="third-party-integration"></a>Külső fél integrációja

| Megoldás | Leírás |
|:---|:---|
| [ITSM](alerts/itsmc-overview.md) | Az IT Service Management-összekötő (ITSMC) lehetővé teszi, hogy az Azure-t egy támogatott IT Service Management- (ITSM-) termékhez/szolgáltatáshoz kapcsolja.  |


## <a name="resources-outside-of-azure"></a>Azure-beli erőforrások
Azure Monitor a következő táblázatban felsorolt módszerekkel adatokat gyűjthet az Azure-n kívüli erőforrásokból.

| Erőforrás | Metódus |
|:---|:---|
| Alkalmazások | Az Azure-n kívüli webalkalmazások figyelése Application Insights. Lásd: [Mi az Application Insights?](./app/app-insights-overview.md). |
| Virtual machines (Virtuális gépek) | Ügynökök használatával adatokat gyűjthet a vendég operációs rendszerről más felhőkörnyezetekben vagy helyszíni virtuális gépeken. Lásd: [A Azure Monitor áttekintése.](agents/agents-overview.md) |
| REST API-ügyfél | Külön API-k érhetők el az adatok bármely ügyféltől Azure Monitor naplókba és metrikákba REST API írására. Lásd: Naplóadatok küldése a Azure Monitor-nek a [naplókhoz](logs/data-collector-api.md) a HTTP Data Collector API-val és Egyéni metrikák küldése egy Azure-erőforráshoz az Azure Monitor-metrikatárolóba a metrikákhoz [REST API](essentials/metrics-store-custom-rest-api.md) használatával. |



## <a name="next-steps"></a>Következő lépések

- További információ a Azure Monitor [adatplatformról,](data-platform.md)amely az elemzések és megoldások által gyűjtött naplókat és metrikákat tárolja.
- Az [Azure-erőforrások monitorozását ismertető oktatóanyagot itt talál:](essentials/tutorial-resource-logs.md).
- Az adatok naplónaplókban való elemzéséhez írjon egy naplólekérdezés [Azure Monitor oktatóanyagot.](essentials/tutorial-resource-logs.md)
- Az adatok metrikákban való elemzéséhez hozzon létre egy metrikadiagramot [Azure Monitor oktatóanyagot.](essentials/tutorial-metrics-explorer.md)

