---
title: Azure Monitor erőforrás-naplók támogatott szolgáltatásai és kategóriái
description: Azure Monitor az Azure-erőforrás-naplók támogatott szolgáltatásainak és az esemény sémájának ismertetése.
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 9a04d0f470522dd4689d604756ffd25e70c5d456
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033146"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Az Azure erőforrás-naplók támogatott kategóriái

> [!NOTE]
> Az erőforrás-naplók korábban diagnosztikai naplóként ismertek. A név 2019 októberében módosult, mivel az Azure Monitor által összegyűjtött naplók típusai többek között az Azure-erőforrást is tartalmazzák.

[Azure monitor az erőforrás-naplókat](../essentials/platform-logs-overview.md) az Azure-szolgáltatások által kibocsátott naplók, amelyek leírják ezeknek a szolgáltatásoknak vagy erőforrásoknak a működését. A Azure Monitoron keresztül elérhető összes erőforrás-napló közös legfelső szintű sémával rendelkezik, és minden szolgáltatás számára rugalmasságot biztosít, hogy egyedi tulajdonságokat bocsát ki a saját eseményeihez.

Az erőforrástípus (a `resourceId` tulajdonságban elérhető) és a `category` séma egyedileg azonosítható kombinációja. A Service-specifikus mezőket tartalmazó összes erőforrás-naplóhoz közös séma tartozik, majd a különböző naplózási kategóriákhoz hozzáadva. További információ: [Common és Service-specifikus séma Azure-beli erőforrás-naplókhoz]()


## <a name="costs"></a>Költségek

Az adatok a Log Analyticsba, az Azure Storage-ba és/vagy az Event Hubbe való küldésével és tárolásával kapcsolatos költségek. Előfordulhat, hogy a költségeket az ilyen helyszínekre való beszerzéshez és a megtartásához kell fizetnie.  Az erőforrás-naplók egy adattípusok, amelyeket elküldhet a következő helyszínekre. 

Az erőforrás-naplók egyes kategóriáinak exportálására további díj vonatkozik. Az exportálási költségekkel rendelkező naplók az alábbi táblázatban láthatók. A díjszabással kapcsolatos további információkért tekintse meg a platform naplók című szakaszt a [Azure monitor díjszabása lapon](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="supported-log-categories-per-resource-type"></a>Támogatott naplózási kategóriák/erőforrás típusok

Az alábbi lista felsorolja az egyes erőforrástípusok számára elérhető naplók típusait. 

Bizonyos kategóriák csak bizonyos típusú erőforrások esetén támogatottak. Ha úgy érzi, hogy hiányzik egy erőforrás, tekintse meg az erőforrás-specifikus dokumentációt. Például a Microsoft. SQL/kiszolgálók/adatbázisok kategóriái nem érhetők el minden típusú adatbázishoz. További információ: [SQL Database diagnosztikai naplózással kapcsolatos információk](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Ha úgy gondolja, hogy valami hiányzik, akkor a cikk alján megnyithatja a GitHub-megjegyzést.
## <a name="microsoftaaddomainservices"></a>Microsoft. HRE/domainServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AccountLogon|AccountLogon|Nem|
|AccountManagement|AccountManagement|Nem|
|DetailTracking|DetailTracking|Nem|
|DirectoryServiceAccess|DirectoryServiceAccess|Nem|
|LogonLogoff|LogonLogoff|Nem|
|ObjectAccess|ObjectAccess|Nem|
|PolicyChange|PolicyChange|Nem|
|PrivilegeUse|PrivilegeUse|Nem|
|SystemSecurity|SystemSecurity|Nem|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Motor|Motor|Nem|
|Szolgáltatás|Szolgáltatás|Nem|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|GatewayLogs|A ApiManagement-átjáróval kapcsolatos naplók|Nem|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|HttpRequest|HTTP-kérelmek|Igen|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ApplicationConsole|Application Console|Nem|
|SystemLogs|Rendszernaplók|Nem|


## <a name="microsoftattestationattestationproviders"></a>Microsoft. igazolás/attestationProviders

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AuditEvent|AuditEvent-üzenet naplójának kategóriája|Nem|
|HIBA|Hibaüzenet naplójának kategóriája|Nem|
|INF|Tájékoztató üzenet naplójának kategóriája|Nem|
|Figyelem|Figyelmeztető üzenet naplójának kategóriája|Nem|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DscNodeStatus|DSC-csomópont állapota|Nem|
|JobLogs|Feladatok naplói|Nem|
|JobStreams|Feladatok adatfolyamai|Nem|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ServiceLog|Szolgáltatási naplók|Nem|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|Nem|
|BaiClusterNodeEvent|BaiClusterNodeEvent|Nem|
|BaiJobEvent|BaiJobEvent|Nem|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockchain/blockchainMembers

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BlockchainApplication|Blockchain-alkalmazás|Nem|
|FabricOrderer|Háló-sorrend|Nem|
|FabricPeer|Háló társ|Nem|
|Proxy|Proxy|Nem|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. Blockchain/cordaMembers

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BlockchainApplication|Blockchain-alkalmazás|Nem|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BotRequest|A csatornáktól érkező kérések a robotba|Nem|
|DependencyRequest|Függőségi kérelmek|Nem|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|WebApplicationFirewallLogs|Webes alkalmazási tűzfal naplófájljai|Nem|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profilok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AzureCdnAccessLog|Azure CDN hozzáférési napló|Nem|
|FrontDoorAccessLog|FrontDoor hozzáférési naplója|Igen|
|FrontDoorHealthProbeLog|FrontDoor állapot mintavételi naplója|Igen|
|FrontDoorWebApplicationFirewallLog|FrontDoor WebApplicationFirewall-napló|Igen|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profilok/végpontok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|CoreAnalytics|Lekéri a végpont metrikáit, például a sávszélességet, a kimenő adatokat stb.|Nem|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|Nem|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Naplók|Nem|
|RequestResponse|Kérelmek és válaszok naplói|Nem|
|Nyomkövetés|Nyomkövetési naplók|Nem|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ChatOperational|Operatív csevegési naplók|Nem|
|SMSOperational|Operatív SMS-naplók|Nem|
|Használat|Használati rekordok|Nem|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ContainerRegistryLoginEvents|Bejelentkezési események|Nem|
|ContainerRegistryRepositoryEvents|RepositoryEvent-naplók|Nem|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|fürt – automéretező|Kubernetes-fürt autoskálázása|Nem|
|őr|őr|Nem|
|Kube – apiserver|Kubernetes API-kiszolgáló|Nem|
|Kube – naplózás|Kubernetes naplózása|Nem|
|Kube – naplózás – rendszergazda|Kubernetes naplózási felügyeleti naplók|Nem|
|Kube – vezérlő – kezelő|Kubernetes Controller Manager|Nem|
|Kube – ütemező|Kubernetes Scheduler|Nem|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AuditLogs|MiniRP-hívások naplófájljai|Nem|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/példányok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Események naplózása|Nem|
|Működik|Működési események|Nem|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. Databricks/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|fiókok|Databricks-fiókok|Nem|
|fürtök|Databricks-fürtök|Nem|
|dbfs|Databricks fájlrendszer|Nem|
|instancePools|Példányok készletei|Nem|
|feladatok|Databricks-feladatok|Nem|
|jegyzetfüzet|Databricks-jegyzetfüzet|Nem|
|titkok|Databricks titkai|Nem|
|sqlPermissions|Databricks SQLPermissions|Nem|
|SSH|Databricks SSH|Nem|
|munkaterület|Databricks-munkaterület|Nem|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. DataCollaboration/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|CollaborationAudit|Együttműködés naplózása|Igen|
|DataAssets|Adategységek|Nem|
|Pipelines|Pipelines|Nem|
|Javaslatok|Javaslatok|Nem|
|Parancsfájlok|Parancsfájlok|Nem|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ActivityRuns|Folyamat-futtatási tevékenység naplója|Nem|
|PipelineRuns|Folyamat-futtatási napló|Nem|
|SSISIntegrationRuntimeLogs|SSIS Integration Runtime-naplók|Nem|
|SSISPackageEventMessageContext|SSIS-csomag – esemény üzenetének környezete|Nem|
|SSISPackageEventMessages|SSIS-csomag – esemény üzenetei|Nem|
|SSISPackageExecutableStatistics|SSIS csomag végrehajtható fájljainak statisztikája|Nem|
|SSISPackageExecutionComponentPhases|SSIS-csomag végrehajtási összetevőjének fázisai|Nem|
|SSISPackageExecutionDataStatistics|SSIS csomag exeution adatok statisztikája|Nem|
|TriggerRuns|Trigger futtatási naplója|Nem|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Naplók|Nem|
|Kérelmek|Kérelmek naplói|Nem|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Naplók|Nem|
|Kérelmek|Kérelmek naplói|Nem|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ReceivedShareSnapshots|Fogadott megosztási Pillanatképek|Nem|
|SentShareSnapshots|Eljuttatott megosztási Pillanatképek|Nem|
|Megosztások|Megosztások|Nem|
|ShareSubscriptions|Előfizetések megosztása|Nem|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|MySqlAuditLogs|MariaDB naplófájlok|Nem|
|MySqlSlowLogs|MariaDB-kiszolgáló naplófájljai|Nem|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|MySqlAuditLogs|MySQL-naplók|Nem|
|MySqlSlowLogs|Lassú MySQL-naplók|Nem|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|MySqlAuditLogs|MySQL-naplók|Nem|
|MySqlSlowLogs|MySQL-kiszolgáló naplófájljai|Nem|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|Nem|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|Nem|
|QueryStoreRuntimeStatistics|PostgreSQL Query Store futásidejű statisztikái|Nem|
|QueryStoreWaitStatistics|PostgreSQL lekérdezési tár várakozási statisztikái|Nem|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|Nem|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Checkpoint|Checkpoint|Nem|
|Hiba|Hiba|Nem|
|Kezelés|Kezelés|Nem|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|Nem|
|Checkpoint|Checkpoint|Nem|
|Kapcsolat|Kapcsolat|Nem|
|Hiba|Hiba|Nem|
|Gazdagép regisztrációja|Gazdagép regisztrációja|Nem|
|Kezelés|Kezelés|Nem|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Checkpoint|Checkpoint|Nem|
|Hiba|Hiba|Nem|
|Adatcsatorna|Adatcsatorna|Nem|
|Kezelés|Kezelés|Nem|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|C2DCommands|C2D parancsok|Nem|
|C2DTwinOperations|C2D – két művelet|Nem|
|Konfigurációk|Konfigurációk|Nem|
|Kapcsolatok|Kapcsolatok|Nem|
|D2CTwinOperations|D2CTwinOperations|Nem|
|DeviceIdentityOperations|Eszköz-identitási műveletek|Nem|
|DeviceStreams|Eszköz streamek (előzetes verzió)|Nem|
|DeviceTelemetry|Eszköztelemetria|Nem|
|DirectMethods|Közvetlen metódusok|Nem|
|DistributedTracing|Elosztott nyomkövetés (előzetes verzió)|Nem|
|FileUploadOperations|Fájlfeltöltés-műveletek|Nem|
|JobsOperations|Feladatok műveletei|Nem|
|Útvonalak|Útvonalak|Nem|
|TwinQueries|Dupla lekérdezés|Nem|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|C2DCommands|C2D parancsok|Nem|
|C2DTwinOperations|C2D – két művelet|Nem|
|Konfigurációk|Konfigurációk|Nem|
|Kapcsolatok|Kapcsolatok|Nem|
|D2CTwinOperations|D2CTwinOperations|Nem|
|DeviceIdentityOperations|Eszköz-identitási műveletek|Nem|
|DeviceStreams|Eszköz streamek (előzetes verzió)|Nem|
|DeviceTelemetry|Eszköztelemetria|Nem|
|DirectMethods|Közvetlen metódusok|Nem|
|DistributedTracing|Elosztott nyomkövetés (előzetes verzió)|Nem|
|FileUploadOperations|Fájlfeltöltés-műveletek|Nem|
|JobsOperations|Feladatok műveletei|Nem|
|Útvonalak|Útvonalak|Nem|
|TwinQueries|Dupla lekérdezés|Nem|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeviceOperations|Eszköz műveletei|Nem|
|ServiceOperations|Szolgáltatási műveletek|Nem|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|Nem|
|EventRoutesOperation|EventRoutesOperation|Nem|
|ModelsOperation|ModelsOperation|Nem|
|QueryOperation|QueryOperation|Nem|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|CassandraRequests|CassandraRequests|Nem|
|ControlPlaneRequests|ControlPlaneRequests|Nem|
|DataPlaneRequests|DataPlaneRequests|Nem|
|GremlinRequests|GremlinRequests|Nem|
|MongoRequests|MongoRequests|Nem|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|Nem|
|PartitionKeyStatistics|PartitionKeyStatistics|Nem|
|QueryRuntimeStatistics|QueryRuntimeStatistics|Nem|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|Nem|
|PublishFailures|Sikertelen naplók közzététele|Nem|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|Nem|
|PublishFailures|Sikertelen naplók közzététele|Nem|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|Nem|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|Nem|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|Nem|
|PublishFailures|Sikertelen naplók közzététele|Nem|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ArchiveLogs|Archiválási naplók|Nem|
|AutoScaleLogs|Naplók automatikus méretezése|Nem|
|CustomerManagedKeyUserLogs|Ügyfél által felügyelt kulcsok naplói|Nem|
|EventHubVNetConnectionEvent|VNet/IP-szűrési kapcsolatok naplófájljai|Nem|
|KafkaCoordinatorLogs|Kafka-koordinátor naplói|Nem|
|KafkaUserErrorLogs|Kafka felhasználói hibák naplói|Nem|
|OperationalLogs|Operatív naplók|Nem|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. kísérletezés/experimentWorkspaces

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Kérés|Kérés|Nem|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/szolgáltatások

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AuditLogs|Naplók|Nem|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/autoscalesettings

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AutoscaleEvaluations|Az autoscale-értékelések|Nem|
|AutoscaleScaleActions|Méretezési műveletek az autoskálázáshoz|Nem|


## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AppAvailabilityResults|Rendelkezésre állási eredmények|Nem|
|AppBrowserTimings|Böngésző időzítése|Nem|
|AppDependencies|Függőségek|Nem|
|AppEvents|Események|Nem|
|AppExceptions|Kivételek|Nem|
|AppMetrics|Mérőszámok|Nem|
|AppPageViews|Lapok nézetei|Nem|
|AppPerformanceCounters|Teljesítményszámlálók|Nem|
|AppRequests|Kérelmek|Nem|
|AppSystemEvents|Rendszeresemények|Nem|
|AppTraces|Hívásláncok|Nem|


## <a name="microsoftiotspacesgraph"></a>Microsoft. IoTSpaces/Graph

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Naplózás|Nem|
|Kimenő forgalom|Kimenő forgalom|Nem|
|Bejövő forgalom|Bejövő forgalom|Nem|
|Működik|Működik|Nem|
|Nyomkövetés|Nyomkövetés|Nem|
|UserDefinedFunction|UserDefinedFunction|Nem|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. kulcstartó/managedhsms

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AuditEvent|Esemény naplózása|Nem|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AuditEvent|Naplók|Nem|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Parancs|Parancs|Nem|
|FailedIngestion|Sikertelen betöltési műveletek|Nem|
|IngestionBatching|Ingestion batching|Nem|
|Lekérdezés|Lekérdezés|Nem|
|SucceededIngestion|Sikeres betöltési műveletek|Nem|
|TableDetails|Táblázat részletei|Nem|
|TableUsageStatistics|Táblázat-használati statisztika|Nem|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|IntegrationAccountTrackingEvents|Integrációs fiók követési eseményei|Nem|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|WorkflowRuntime|Munkafolyamat futásidejű diagnosztikai eseményei|Nem|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|Nem|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|Nem|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|Nem|
|AmlComputeJobEvent|AmlComputeJobEvent|Nem|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|Nem|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|KeyDeliveryRequests|Kulcsok kézbesítésére vonatkozó kérelmek|Nem|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ApplicationGatewayAccessLog|Hozzáférési napló Application Gateway|Nem|
|ApplicationGatewayFirewallLog|Application Gateway tűzfal naplója|Nem|
|ApplicationGatewayPerformanceLog|Application Gateway Teljesítménynapló|Nem|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AzureFirewallApplicationRule|Azure Firewall alkalmazási szabály|Nem|
|AzureFirewallDnsProxy|Azure Firewall DNS-proxy|Nem|
|AzureFirewallNetworkRule|Azure Firewall hálózati szabály|Nem|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BastionAuditLogs|Megerősített naplók|Nem|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|PeeringRouteLog|Egyenrangú útválasztási táblázat naplói|Nem|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|FrontdoorAccessLog|Frontdoor hozzáférési naplója|Nem|
|FrontdoorWebApplicationFirewallLog|Frontdoor webalkalmazási tűzfal naplója|Nem|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|LoadBalancerAlertEvent|Riasztási események Load Balancer|Nem|
|LoadBalancerProbeHealthStatus|Load Balancer mintavétel állapotának állapota|Nem|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|NetworkSecurityGroupEvent|Hálózati biztonsági csoport eseménye|Nem|
|NetworkSecurityGroupFlowEvent|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|Nem|
|NetworkSecurityGroupRuleCounter|Hálózati biztonsági csoport szabályának számlálója|Nem|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|Nem|
|IKEDiagnosticLog|IKE diagnosztikai naplók|Nem|
|P2SDiagnosticLog|P2S diagnosztikai naplók|Nem|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/nyilvános IP

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DDoSMitigationFlowLogs|A DDoS-elhárítási döntések folyamatának naplói|Nem|
|DDoSMitigationReports|A DDoS enyhítésének jelentései|Nem|
|DDoSProtectionNotifications|DDoS Protection-értesítések|Nem|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ProbeHealthStatusEvents|Traffic Manager mintavételi állapot eredményeinek eseménye|Nem|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|Nem|
|IKEDiagnosticLog|IKE diagnosztikai naplók|Nem|
|P2SDiagnosticLog|P2S diagnosztikai naplók|Nem|
|RouteDiagnosticLog|Diagnosztikai naplók irányítása|Nem|
|TunnelDiagnosticLog|Alagutak diagnosztikai naplói|Nem|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|VMProtectionAlerts|VM-védelmi riasztások|Nem|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|Nem|
|IKEDiagnosticLog|IKE diagnosztikai naplók|Nem|
|RouteDiagnosticLog|Diagnosztikai naplók irányítása|Nem|
|TunnelDiagnosticLog|Alagutak diagnosztikai naplói|Nem|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft. NotificationHubs/névterek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|OperationalLogs|Operatív naplók|Nem|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Naplók|Nem|


## <a name="microsoftpowerbitenants"></a>Microsoft. PowerBI/bérlők

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Motor|Motor|Nem|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. PowerBI/bérlők/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Motor|Motor|Nem|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Motor|Motor|Nem|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Nem|


## <a name="microsoftpurviewaccounts"></a>Microsoft. hatáskörébe/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Nem|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. Recoveryservices szolgáltatónál/tárolók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AddonAzureBackupAlerts|Addon Azure Backup riasztási adatkezelési|Nem|
|AddonAzureBackupJobs|Addon Azure Backup feladatok adatai|Nem|
|AddonAzureBackupPolicy|Addon Azure Backup házirend-adatbázis|Nem|
|AddonAzureBackupProtectedInstance|Addon Azure Backup védett példányok adatvédelme|Nem|
|AddonAzureBackupStorage|Addon Azure Backup Storage-beli adattárak|Nem|
|AzureBackupReport|Jelentéskészítési adatgyűjtés Azure Backup|Nem|
|AzureSiteRecoveryEvents|Események Azure Site Recovery|Nem|
|AzureSiteRecoveryJobs|Azure Site Recovery feladatok|Nem|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery védett lemez adatváltozása|Nem|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery helyreállítási pontok|Nem|
|AzureSiteRecoveryReplicatedItems|Replikált elemek Azure Site Recovery|Nem|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery replikációs adatok feltöltési sebessége|Nem|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery replikációs statisztika|Nem|
|CoreAzureBackup|Alapvető Azure Backup-adathalmazok|Nem|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/névterek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|HybridConnectionsEvent|HybridConnections események|Nem|
|HybridConnectionsLogs|HybridConnectionsLogs|Nem|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|OperationLogs|Műveleti naplók|Nem|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|OperationalLogs|Operatív naplók|Nem|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AllLogs|Az Azure Signaler szolgáltatás naplói.|Nem|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DevOpsOperationsAudit|Devops-műveletek naplófájljai|Nem|
|ResourceUsageStats|Erőforrás-használati statisztika|Nem|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|Nem|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/adatbázisok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Hibák|Hibák|Nem|
|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|Nem|
|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|Nem|
|SQLInsights|SQL-ismeretek|Nem|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AutomaticTuning|Automatikus hangolás|Nem|
|Blokkok|Blokkok|Nem|
|DatabaseWaitStatistics|Adatbázis várakozási statisztikája|Nem|
|Holtpontok|Holtpontok|Nem|
|DevOpsOperationsAudit|Devops-műveletek naplófájljai|Nem|
|DmsWorkers|DMS-feldolgozók|Nem|
|Hibák|Hibák|Nem|
|ExecRequests|Exec-kérelmek|Nem|
|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|Nem|
|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|Nem|
|RequestSteps|Kérelem lépései|Nem|
|SQLInsights|SQL-ismeretek|Nem|
|SqlRequests|SQL-kérelmek|Nem|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|Nem|
|Időtúllépések|Időtúllépések|Nem|
|Megvárja|Megvárja|Nem|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|StorageDelete|StorageDelete|Igen|
|StorageRead|StorageRead|Igen|
|StorageWrite|StorageWrite|Igen|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|StorageDelete|StorageDelete|Igen|
|StorageRead|StorageRead|Igen|
|StorageWrite|StorageWrite|Igen|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|StorageDelete|StorageDelete|Igen|
|StorageRead|StorageRead|Igen|
|StorageWrite|StorageWrite|Igen|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|StorageDelete|StorageDelete|Igen|
|StorageRead|StorageRead|Igen|
|StorageWrite|StorageWrite|Igen|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Tartalomkészítés|Tartalomkészítés|Nem|
|Futtatási|Futtatási|Nem|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. szinapszis/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BuiltinSqlReqsEnded|A beépített SQL Pool-kérelmek véget ért|Nem|
|GatewayApiRequests|Szinapszis Gateway API-kérelmek|Nem|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|Nem|
|SynapseRbacOperations|Szinapszis RBAC-műveletek|Nem|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. szinapszis/munkaterületek/bigDataPools

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BigDataPoolAppsEnded|A Big adattár-alkalmazások véget ért|Nem|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. szinapszis/munkaterületek/sqlPools

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DmsWorkers|DMS-feldolgozók|Nem|
|ExecRequests|Exec-kérelmek|Nem|
|RequestSteps|Kérelem lépései|Nem|
|SqlRequests|SQL-kérelmek|Nem|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|Nem|
|Megvárja|Megvárja|Nem|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/Environments

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Bejövő forgalom|Bejövő forgalom|Nem|
|Kezelés|Kezelés|Nem|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Bejövő forgalom|Bejövő forgalom|Nem|
|Kezelés|Kezelés|Nem|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment platform naplói|Nem|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|A víruskereső naplófájljainak jelentése|Nem|
|AppServiceAppLogs|App Service alkalmazás naplófájljai|Nem|
|AppServiceAuditLogs|Hozzáférés-naplózási naplók|Nem|
|AppServiceConsoleLogs|App Service konzol naplófájljai|Nem|
|AppServiceFileAuditLogs|Webhely tartalmának változási naplófájljai|Nem|
|AppServiceHTTPLogs|HTTP-naplók|Nem|
|AppServiceIPSecAuditLogs|IPSecurity naplófájlok|Nem|
|AppServicePlatformLogs|App Service platform naplói|Nem|
|FunctionAppLogs|Function Application-naplók|Nem|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|A víruskereső naplófájljainak jelentése|Nem|
|AppServiceAppLogs|App Service alkalmazás naplófájljai|Nem|
|AppServiceAuditLogs|Hozzáférés-naplózási naplók|Nem|
|AppServiceConsoleLogs|App Service konzol naplófájljai|Nem|
|AppServiceFileAuditLogs|Webhely tartalmának változási naplófájljai|Nem|
|AppServiceHTTPLogs|HTTP-naplók|Nem|
|AppServiceIPSecAuditLogs|IPSecurity naplófájlok|Nem|
|AppServicePlatformLogs|App Service platform naplói|Nem|
|FunctionAppLogs|Function Application-naplók|Nem|



## <a name="next-steps"></a>Következő lépések

* [További információ az erőforrás-naplókról](../essentials/platform-logs-overview.md)
* [Stream erőforrás-erőforrás naplófájljai **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Az erőforrás-napló diagnosztikai beállításainak módosítása a Azure Monitor használatával REST API](/rest/api/monitor/diagnosticsettings)
* [Naplók elemzése az Azure Storage-ból Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

