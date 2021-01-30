---
title: Azure Monitor erőforrás-naplók támogatott szolgáltatásai és kategóriái
description: Azure Monitor az Azure-erőforrás-naplók támogatott szolgáltatásainak és az esemény sémájának ismertetése.
ms.subservice: logs
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 02488e1a3ff26acf9ff318a2d5c09115aaba8df9
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070746"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Az Azure erőforrás-naplók támogatott kategóriái

> [!NOTE]
> Az erőforrás-naplók korábban diagnosztikai naplóként ismertek. A név 2019 októberében módosult, mivel az Azure Monitor által összegyűjtött naplók típusai többek között az Azure-erőforrást is tartalmazzák.

[Azure monitor az erőforrás-naplókat](./platform-logs-overview.md) az Azure-szolgáltatások által kibocsátott naplók, amelyek leírják ezeknek a szolgáltatásoknak vagy erőforrásoknak a működését. A Azure Monitoron keresztül elérhető összes erőforrás-napló közös legfelső szintű sémával rendelkezik, és minden szolgáltatás számára rugalmasságot biztosít, hogy egyedi tulajdonságokat bocsát ki a saját eseményeihez.

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
|AccountLogon|AccountLogon|No|
|AccountManagement|AccountManagement|No|
|DetailTracking|DetailTracking|No|
|DirectoryServiceAccess|DirectoryServiceAccess|No|
|LogonLogoff|LogonLogoff|No|
|ObjectAccess|ObjectAccess|No|
|PolicyChange|PolicyChange|No|
|PrivilegeUse|PrivilegeUse|No|
|SystemSecurity|SystemSecurity|No|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Motor|Motor|No|
|Szolgáltatás|Szolgáltatás|No|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|GatewayLogs|A ApiManagement-átjáróval kapcsolatos naplók|No|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|HttpRequest|HTTP-kérelmek|Yes|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ApplicationConsole|Application Console|No|
|SystemLogs|Rendszernaplók|No|


## <a name="microsoftattestationattestationproviders"></a>Microsoft. igazolás/attestationProviders

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AuditEvent|AuditEvent-üzenet naplójának kategóriája|No|
|HIBA|Hibaüzenet naplójának kategóriája|No|
|INF|Tájékoztató üzenet naplójának kategóriája|No|
|Figyelem|Figyelmeztető üzenet naplójának kategóriája|No|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DscNodeStatus|DSC-csomópont állapota|No|
|JobLogs|Feladatok naplói|No|
|JobStreams|Feladatok adatfolyamai|No|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ServiceLog|Szolgáltatási naplók|No|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|No|
|BaiClusterNodeEvent|BaiClusterNodeEvent|No|
|BaiJobEvent|BaiJobEvent|No|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockchain/blockchainMembers

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BlockchainApplication|Blockchain-alkalmazás|No|
|FabricOrderer|Háló-sorrend|No|
|FabricPeer|Háló társ|No|
|Proxy|Proxy|No|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. Blockchain/cordaMembers

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BlockchainApplication|Blockchain-alkalmazás|No|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BotRequest|A csatornáktól érkező kérések a robotba|No|
|DependencyRequest|Függőségi kérelmek|No|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|WebApplicationFirewallLogs|Webes alkalmazási tűzfal naplófájljai|No|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profilok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AzureCdnAccessLog|Azure CDN hozzáférési napló|No|
|FrontDoorAccessLog|FrontDoor hozzáférési naplója|Yes|
|FrontDoorHealthProbeLog|FrontDoor állapot mintavételi naplója|Yes|
|FrontDoorWebApplicationFirewallLog|FrontDoor WebApplicationFirewall-napló|Yes|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profilok/végpontok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|CoreAnalytics|Lekéri a végpont metrikáit, például a sávszélességet, a kimenő adatokat stb.|No|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|No|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Naplók|No|
|RequestResponse|Kérelmek és válaszok naplói|No|
|Nyomkövetés|Nyomkövetési naplók|No|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ChatOperational|Operatív csevegési naplók|No|
|SMSOperational|Operatív SMS-naplók|No|
|Használat|Használati rekordok|No|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ContainerRegistryLoginEvents|Bejelentkezési események|No|
|ContainerRegistryRepositoryEvents|RepositoryEvent-naplók|No|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|fürt – automéretező|Kubernetes-fürt autoskálázása|No|
|őr|őr|No|
|Kube – apiserver|Kubernetes API-kiszolgáló|No|
|Kube – naplózás|Kubernetes naplózása|No|
|Kube – naplózás – rendszergazda|Kubernetes naplózási felügyeleti naplók|No|
|Kube – vezérlő – kezelő|Kubernetes Controller Manager|No|
|Kube – ütemező|Kubernetes Scheduler|No|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AuditLogs|MiniRP-hívások naplófájljai|No|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/példányok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Események naplózása|No|
|Működik|Működési események|No|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. Databricks/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|fiókok|Databricks-fiókok|No|
|fürtök|Databricks-fürtök|No|
|dbfs|Databricks fájlrendszer|No|
|instancePools|Példányok készletei|No|
|feladatok|Databricks-feladatok|No|
|jegyzetfüzet|Databricks-jegyzetfüzet|No|
|titkok|Databricks titkai|No|
|sqlPermissions|Databricks SQLPermissions|No|
|SSH|Databricks SSH|No|
|munkaterület|Databricks-munkaterület|No|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. DataCollaboration/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|CollaborationAudit|Együttműködés naplózása|Yes|
|DataAssets|Adategységek|No|
|Pipelines|Pipelines|No|
|Javaslatok|Javaslatok|No|
|Parancsfájlok|Parancsfájlok|No|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ActivityRuns|Folyamat-futtatási tevékenység naplója|No|
|PipelineRuns|Folyamat-futtatási napló|No|
|SSISIntegrationRuntimeLogs|SSIS Integration Runtime-naplók|No|
|SSISPackageEventMessageContext|SSIS-csomag – esemény üzenetének környezete|No|
|SSISPackageEventMessages|SSIS-csomag – esemény üzenetei|No|
|SSISPackageExecutableStatistics|SSIS csomag végrehajtható fájljainak statisztikája|No|
|SSISPackageExecutionComponentPhases|SSIS-csomag végrehajtási összetevőjének fázisai|No|
|SSISPackageExecutionDataStatistics|SSIS csomag exeution adatok statisztikája|No|
|TriggerRuns|Trigger futtatási naplója|No|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Naplók|No|
|Kérelmek|Kérelmek naplói|No|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Naplók|No|
|Kérelmek|Kérelmek naplói|No|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ReceivedShareSnapshots|Fogadott megosztási Pillanatképek|No|
|SentShareSnapshots|Eljuttatott megosztási Pillanatképek|No|
|Megosztások|Megosztások|No|
|ShareSubscriptions|Előfizetések megosztása|No|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|MySqlAuditLogs|MariaDB naplófájlok|No|
|MySqlSlowLogs|MariaDB-kiszolgáló naplófájljai|No|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|MySqlAuditLogs|MySQL-naplók|No|
|MySqlSlowLogs|Lassú MySQL-naplók|No|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|MySqlAuditLogs|MySQL-naplók|No|
|MySqlSlowLogs|MySQL-kiszolgáló naplófájljai|No|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|No|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|No|
|QueryStoreRuntimeStatistics|PostgreSQL Query Store futásidejű statisztikái|No|
|QueryStoreWaitStatistics|PostgreSQL lekérdezési tár várakozási statisztikái|No|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|No|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Checkpoint|Checkpoint|No|
|Hiba|Hiba|No|
|Kezelés|Kezelés|No|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|No|
|Checkpoint|Checkpoint|No|
|Kapcsolat|Kapcsolat|No|
|Hiba|Hiba|No|
|Gazdagép regisztrációja|Gazdagép regisztrációja|No|
|Kezelés|Kezelés|No|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Checkpoint|Checkpoint|No|
|Hiba|Hiba|No|
|Adatcsatorna|Adatcsatorna|No|
|Kezelés|Kezelés|No|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|C2DCommands|C2D parancsok|No|
|C2DTwinOperations|C2D – két művelet|No|
|Konfigurációk|Konfigurációk|No|
|Kapcsolatok|Kapcsolatok|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Eszköz-identitási műveletek|No|
|DeviceStreams|Eszköz streamek (előzetes verzió)|No|
|DeviceTelemetry|Eszköztelemetria|No|
|DirectMethods|Közvetlen metódusok|No|
|DistributedTracing|Elosztott nyomkövetés (előzetes verzió)|No|
|FileUploadOperations|Fájlfeltöltés-műveletek|No|
|JobsOperations|Feladatok műveletei|No|
|Útvonalak|Útvonalak|No|
|TwinQueries|Dupla lekérdezés|No|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|C2DCommands|C2D parancsok|No|
|C2DTwinOperations|C2D – két művelet|No|
|Konfigurációk|Konfigurációk|No|
|Kapcsolatok|Kapcsolatok|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Eszköz-identitási műveletek|No|
|DeviceStreams|Eszköz streamek (előzetes verzió)|No|
|DeviceTelemetry|Eszköztelemetria|No|
|DirectMethods|Közvetlen metódusok|No|
|DistributedTracing|Elosztott nyomkövetés (előzetes verzió)|No|
|FileUploadOperations|Fájlfeltöltés-műveletek|No|
|JobsOperations|Feladatok műveletei|No|
|Útvonalak|Útvonalak|No|
|TwinQueries|Dupla lekérdezés|No|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeviceOperations|Eszköz műveletei|No|
|ServiceOperations|Szolgáltatási műveletek|No|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|No|
|EventRoutesOperation|EventRoutesOperation|No|
|ModelsOperation|ModelsOperation|No|
|QueryOperation|QueryOperation|No|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|CassandraRequests|CassandraRequests|No|
|ControlPlaneRequests|ControlPlaneRequests|No|
|DataPlaneRequests|DataPlaneRequests|No|
|GremlinRequests|GremlinRequests|No|
|MongoRequests|MongoRequests|No|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|No|
|PartitionKeyStatistics|PartitionKeyStatistics|No|
|QueryRuntimeStatistics|QueryRuntimeStatistics|No|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|No|
|PublishFailures|Sikertelen naplók közzététele|No|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|No|
|PublishFailures|Sikertelen naplók közzététele|No|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|No|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|No|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|No|
|PublishFailures|Sikertelen naplók közzététele|No|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ArchiveLogs|Archiválási naplók|No|
|AutoScaleLogs|Naplók automatikus méretezése|No|
|CustomerManagedKeyUserLogs|Ügyfél által felügyelt kulcsok naplói|No|
|EventHubVNetConnectionEvent|VNet/IP-szűrési kapcsolatok naplófájljai|No|
|KafkaCoordinatorLogs|Kafka-koordinátor naplói|No|
|KafkaUserErrorLogs|Kafka felhasználói hibák naplói|No|
|OperationalLogs|Operatív naplók|No|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. kísérletezés/experimentWorkspaces

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Kérés|Kérés|No|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/szolgáltatások

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AuditLogs|Naplók|No|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/autoscalesettings

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AutoscaleEvaluations|Az autoscale-értékelések|No|
|AutoscaleScaleActions|Méretezési műveletek az autoskálázáshoz|No|


## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AppAvailabilityResults|Rendelkezésre állási eredmények|No|
|AppBrowserTimings|Böngésző időzítése|No|
|AppDependencies|Függőségek|No|
|AppEvents|Események|No|
|AppExceptions|Kivételek|No|
|AppMetrics|Mérőszámok|No|
|AppPageViews|Lapok nézetei|No|
|AppPerformanceCounters|Teljesítményszámlálók|No|
|AppRequests|Kérelmek|No|
|AppSystemEvents|Rendszeresemények|No|
|AppTraces|Hívásláncok|No|


## <a name="microsoftiotspacesgraph"></a>Microsoft. IoTSpaces/Graph

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Naplózás|No|
|Kimenő forgalom|Kimenő forgalom|No|
|Bejövő forgalom|Bejövő forgalom|No|
|Működik|Működik|No|
|Nyomkövetés|Nyomkövetés|No|
|UserDefinedFunction|UserDefinedFunction|No|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. kulcstartó/managedhsms

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AuditEvent|Esemény naplózása|No|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AuditEvent|Naplók|No|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Parancs|Parancs|No|
|FailedIngestion|Sikertelen betöltési műveletek|No|
|IngestionBatching|Ingestion batching|No|
|Lekérdezés|Lekérdezés|No|
|SucceededIngestion|Sikeres betöltési műveletek|No|
|TableDetails|Táblázat részletei|No|
|TableUsageStatistics|Táblázat-használati statisztika|No|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|IntegrationAccountTrackingEvents|Integrációs fiók követési eseményei|No|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|WorkflowRuntime|Munkafolyamat futásidejű diagnosztikai eseményei|No|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|No|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|No|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|No|
|AmlComputeJobEvent|AmlComputeJobEvent|No|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|No|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|KeyDeliveryRequests|Kulcsok kézbesítésére vonatkozó kérelmek|No|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ApplicationGatewayAccessLog|Hozzáférési napló Application Gateway|No|
|ApplicationGatewayFirewallLog|Application Gateway tűzfal naplója|No|
|ApplicationGatewayPerformanceLog|Application Gateway Teljesítménynapló|No|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AzureFirewallApplicationRule|Azure Firewall alkalmazási szabály|No|
|AzureFirewallDnsProxy|Azure Firewall DNS-proxy|No|
|AzureFirewallNetworkRule|Azure Firewall hálózati szabály|No|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BastionAuditLogs|Megerősített naplók|No|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|PeeringRouteLog|Egyenrangú útválasztási táblázat naplói|No|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|FrontdoorAccessLog|Frontdoor hozzáférési naplója|No|
|FrontdoorWebApplicationFirewallLog|Frontdoor webalkalmazási tűzfal naplója|No|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|LoadBalancerAlertEvent|Riasztási események Load Balancer|No|
|LoadBalancerProbeHealthStatus|Load Balancer mintavétel állapotának állapota|No|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|NetworkSecurityGroupEvent|Hálózati biztonsági csoport eseménye|No|
|NetworkSecurityGroupFlowEvent|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|No|
|NetworkSecurityGroupRuleCounter|Hálózati biztonsági csoport szabályának számlálója|No|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|No|
|IKEDiagnosticLog|IKE diagnosztikai naplók|No|
|P2SDiagnosticLog|P2S diagnosztikai naplók|No|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/nyilvános IP

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DDoSMitigationFlowLogs|A DDoS-elhárítási döntések folyamatának naplói|No|
|DDoSMitigationReports|A DDoS enyhítésének jelentései|No|
|DDoSProtectionNotifications|DDoS Protection-értesítések|No|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ProbeHealthStatusEvents|Traffic Manager mintavételi állapot eredményeinek eseménye|No|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|No|
|IKEDiagnosticLog|IKE diagnosztikai naplók|No|
|P2SDiagnosticLog|P2S diagnosztikai naplók|No|
|RouteDiagnosticLog|Diagnosztikai naplók irányítása|No|
|TunnelDiagnosticLog|Alagutak diagnosztikai naplói|No|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|VMProtectionAlerts|VM-védelmi riasztások|No|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|No|
|IKEDiagnosticLog|IKE diagnosztikai naplók|No|
|RouteDiagnosticLog|Diagnosztikai naplók irányítása|No|
|TunnelDiagnosticLog|Alagutak diagnosztikai naplói|No|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft. NotificationHubs/névterek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|OperationalLogs|Operatív naplók|No|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Naplózás|Naplók|No|


## <a name="microsoftpowerbitenants"></a>Microsoft. PowerBI/bérlők

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Motor|Motor|No|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. PowerBI/bérlők/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Motor|Motor|No|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Motor|Motor|No|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftpurviewaccounts"></a>Microsoft. hatáskörébe/fiókok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. Recoveryservices szolgáltatónál/tárolók

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AddonAzureBackupAlerts|Addon Azure Backup riasztási adatkezelési|No|
|AddonAzureBackupJobs|Addon Azure Backup feladatok adatai|No|
|AddonAzureBackupPolicy|Addon Azure Backup házirend-adatbázis|No|
|AddonAzureBackupProtectedInstance|Addon Azure Backup védett példányok adatvédelme|No|
|AddonAzureBackupStorage|Addon Azure Backup Storage-beli adattárak|No|
|AzureBackupReport|Jelentéskészítési adatgyűjtés Azure Backup|No|
|AzureSiteRecoveryEvents|Események Azure Site Recovery|No|
|AzureSiteRecoveryJobs|Azure Site Recovery feladatok|No|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery védett lemez adatváltozása|No|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery helyreállítási pontok|No|
|AzureSiteRecoveryReplicatedItems|Replikált elemek Azure Site Recovery|No|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery replikációs adatok feltöltési sebessége|No|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery replikációs statisztika|No|
|CoreAzureBackup|Alapvető Azure Backup-adathalmazok|No|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/névterek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|HybridConnectionsEvent|HybridConnections események|No|
|HybridConnectionsLogs|HybridConnectionsLogs|No|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|OperationLogs|Műveleti naplók|No|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|OperationalLogs|Operatív naplók|No|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AllLogs|Az Azure Signaler szolgáltatás naplói.|No|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DevOpsOperationsAudit|Devops-műveletek naplófájljai|No|
|ResourceUsageStats|Erőforrás-használati statisztika|No|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|No|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/adatbázisok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Hibák|Hibák|No|
|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|No|
|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|No|
|SQLInsights|SQL-ismeretek|No|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AutomaticTuning|Automatikus hangolás|No|
|Blokkok|Blokkok|No|
|DatabaseWaitStatistics|Adatbázis várakozási statisztikája|No|
|Holtpontok|Holtpontok|No|
|DevOpsOperationsAudit|Devops-műveletek naplófájljai|No|
|DmsWorkers|DMS-feldolgozók|No|
|Hibák|Hibák|No|
|ExecRequests|Exec-kérelmek|No|
|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|No|
|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|No|
|RequestSteps|Kérelem lépései|No|
|SQLInsights|SQL-ismeretek|No|
|SqlRequests|SQL-kérelmek|No|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|No|
|Időtúllépések|Időtúllépések|No|
|Megvárja|Megvárja|No|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Tartalomkészítés|Tartalomkészítés|No|
|Futtatási|Futtatási|No|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. szinapszis/munkaterületek

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BuiltinSqlReqsEnded|A beépített SQL Pool-kérelmek véget ért|No|
|GatewayApiRequests|Szinapszis Gateway API-kérelmek|No|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|No|
|SynapseRbacOperations|Szinapszis RBAC-műveletek|No|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. szinapszis/munkaterületek/bigDataPools

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|BigDataPoolAppsEnded|A Big adattár-alkalmazások véget ért|No|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. szinapszis/munkaterületek/sqlPools

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|DmsWorkers|DMS-feldolgozók|No|
|ExecRequests|Exec-kérelmek|No|
|RequestSteps|Kérelem lépései|No|
|SqlRequests|SQL-kérelmek|No|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|No|
|Megvárja|Megvárja|No|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/Environments

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Bejövő forgalom|Bejövő forgalom|No|
|Kezelés|Kezelés|No|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|Bejövő forgalom|Bejövő forgalom|No|
|Kezelés|Kezelés|No|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment platform naplói|No|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|A víruskereső naplófájljainak jelentése|No|
|AppServiceAppLogs|App Service alkalmazás naplófájljai|No|
|AppServiceAuditLogs|Hozzáférés-naplózási naplók|No|
|AppServiceConsoleLogs|App Service konzol naplófájljai|No|
|AppServiceFileAuditLogs|Webhely tartalmának változási naplófájljai|No|
|AppServiceHTTPLogs|HTTP-naplók|No|
|AppServiceIPSecAuditLogs|IPSecurity naplófájlok|No|
|AppServicePlatformLogs|App Service platform naplói|No|
|FunctionAppLogs|Function Application-naplók|No|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots

|Kategória|Kategória megjelenítendő neve|Exportálandó költségek|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|A víruskereső naplófájljainak jelentése|No|
|AppServiceAppLogs|App Service alkalmazás naplófájljai|No|
|AppServiceAuditLogs|Hozzáférés-naplózási naplók|No|
|AppServiceConsoleLogs|App Service konzol naplófájljai|No|
|AppServiceFileAuditLogs|Webhely tartalmának változási naplófájljai|No|
|AppServiceHTTPLogs|HTTP-naplók|No|
|AppServiceIPSecAuditLogs|IPSecurity naplófájlok|No|
|AppServicePlatformLogs|App Service platform naplói|No|
|FunctionAppLogs|Function Application-naplók|No|



## <a name="next-steps"></a>Következő lépések

* [További információ az erőforrás-naplókról](./platform-logs-overview.md)
* [Stream erőforrás-erőforrás naplófájljai **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Az erőforrás-napló diagnosztikai beállításainak módosítása a Azure Monitor használatával REST API](/rest/api/monitor/diagnosticsettings)
* [Naplók elemzése az Azure Storage-ból Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

