---
title: Erőforrás-szolgáltatók az Azure-szolgáltatások által
description: Felsorolja a Azure Resource Manager összes erőforrás-szolgáltatói névterét, és megjeleníti az adott névtérhez tartozó Azure-szolgáltatást.
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: d8239a9305560eb77ac2c714b993373f0d5b16fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023010"
---
# <a name="resource-providers-for-azure-services"></a>Erőforrás-szolgáltatók az Azure-szolgáltatásokhoz

Ez a cikk bemutatja, hogyan képezhetők le az erőforrás-szolgáltatói névterek az Azure-szolgáltatásokhoz. Ha nem ismeri az erőforrás-szolgáltatót, tekintse [meg az erőforrás-szolgáltató keresése](#find-resource-provider)című témakört.

## <a name="match-resource-provider-to-service"></a>Erőforrás-szolgáltató egyeztetése a szolgáltatással

Az előfizetéshez tartozó, **regisztráltként** megjelölt erőforrás-szolgáltatók alapértelmezés szerint regisztrálva vannak. További információ: [regisztráció](#registration).

| Erőforrás-szolgáltatói névtér | Azure-szolgáltatás |
| --------------------------- | ------------- |
| Microsoft. HRE | [Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/index.yml) |
| Microsoft. addons | Core |
| Microsoft. ADHybridHealthService – [regisztrált](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft. Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft. AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft. ApiManagement | [API Management](../../api-management/index.yml) |
| Microsoft. AppConfiguration | [Azure App Configuration](../../azure-app-configuration/index.yml) |
| Microsoft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/overview.md) |
| Microsoft. igazolás | Azure-igazolási szolgáltatás |
| Microsoft. Authorization – [regisztrált](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. Automation | [Automatizálás](../../automation/index.yml) |
| Microsoft. AutonomousSystems | [Autonóm rendszerek](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft. AVS | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft. AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureArcData | Azure Arc-kompatibilis adatszolgáltatások |
| Microsoft. AzureData | SQL Server beállításjegyzék |
| Microsoft. AzureStack | Core |
| Microsoft. AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft. számlázás – [regisztrált](#registration) | [Költségkezelés és számlázás](/azure/billing/) |
| Microsoft. BingMaps | [Bing Térképek](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft. Blockchain | [Azure Blockchain Service](../../blockchain/workbench/index.yml) |
| Microsoft. BlockchainTokens | [Azure Blockchain Tokens](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft. Blueprint | [Azure Blueprints](../../governance/blueprints/index.yml) |
| Microsoft. BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft. cache | [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) |
| Microsoft. Capacity | Core |
| Microsoft. CDN | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft. CertificateRegistration | [Tanúsítványok App Service](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Klasszikus üzembe helyezési modell virtuális gép |
| Microsoft. ClassicInfrastructureMigrate | Klasszikus üzembe helyezési modell migrálása |
| Microsoft. ClassicNetwork | Klasszikus üzembe helyezési modell virtuális hálózata |
| Microsoft. ClassicStorage | Klasszikus üzembe helyezési modell tárterülete |
| Microsoft. ClassicSubscription – [regisztrált](#registration) | Klasszikus üzemi modell |
| Microsoft. CognitiveServices | [Cognitive Services](../../cognitive-services/index.yml) |
| Microsoft. Commerce – [regisztrált](#registration) | Core |
| Microsoft.Compute | [Virtual Machines](../../virtual-machines/index.yml)<br />[Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml) |
| Microsoft. fogyasztás – [regisztrált](#registration) | [Költségkezelés](/azure/cost-management/) |
| Microsoft. ContainerInstance | [Tárolópéldányok](../../container-instances/index.yml) |
| Microsoft. ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft. Tárolószolgáltatás | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft. CostManagement – [regisztrált](#registration) | [Költségkezelés](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Költségkezelés](/azure/cost-management/) |
| Microsoft. CustomerLockbox | [Ügyfélszéf a Microsoft Azure-hoz](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft. CustomProviders | [Azure Custom Providers](../custom-providers/overview.md) |
| Microsoft. DataBox | [Azure Data Box](../../databox/index.yml) |
| Microsoft. DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft. Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft. DataCatalog | [Data Catalog](../../data-catalog/index.yml) |
| Microsoft. DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft. DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft. Data Lake Store | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft. DataMigration | [Azure Database Migration Service](../../dms/index.yml) |
| Microsoft. DataProtection | Adatvédelem |
| Microsoft. DataShare | [Azure Data Share](../../data-share/index.yml) |
| Microsoft. DBforMariaDB | [Azure Database for MariaDB](../../mariadb/index.yml) |
| Microsoft. DBforMySQL | [Azure Database for MySQL](../../mysql/index.yml) |
| Microsoft. DBforPostgreSQL | [Azure Database for PostgreSQL](../../postgresql/index.yml) |
| Microsoft. DeploymentManager | [Azure-Deployment Manager](../templates/deployment-manager-overview.md) |
| Microsoft. DesktopVirtualization | [Windows virtuális asztal](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Azure IoT Hub](../../iot-hub/index.yml)<br />[Azure IoT Hub Device Provisioning Service](../../iot-dps/index.yml) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft. DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft. segédösszetevője | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft. DomainRegistration | [APP SERVICE](../../app-service/index.yml) |
| Microsoft. DynamicsLcs | [Életciklus-szolgáltatások](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft. EnterpriseKnowledgeGraph | Enterprise Knowledge Graph |
| Microsoft. EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft. EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft. features – [regisztrált](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. HanaOnAzure | [SAP HANA on Azure Large Instances](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft. HardwareSecurityModules | [Azure Dedicated HSM](../../dedicated-hsm/index.yml) |
| Microsoft. HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [Azure API for FHIR](../../healthcare-apis/fhir/index.yml) |
| Microsoft. HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft. HybridData | [StorSimple](../../storsimple/index.yml) |
| Microsoft. HybridNetwork  | [Saját peremhálózati zónák](../../networking/edge-zones-overview.md) |
| Microsoft. ImportExport | [Azure Import/Export](../../import-export/storage-import-export-service.md) |
| Microsoft. bepillantások | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft. IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. kulcstartó | [Key Vault](../../key-vault/index.yml) |
| Microsoft. Kubernetes | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft. KubernetesConfiguration | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft. LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft. MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. Maintenance | [Azure-karbantartás](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft. ManagedIdentity | [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedNetwork | A Pásti Services által felügyelt virtuális hálózatok |
| Microsoft. ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft. Management | [Management Groups](../../governance/management-groups/index.yml) |
| Microsoft. Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft. Marketplace | Core |
| Microsoft. MarketplaceApps | Core |
| Microsoft. MarketplaceOrdering – [regisztrált](#registration) | Core |
| Microsoft. Media | [Media Services](../../media-services/index.yml) |
| Microsoft. Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/overview.md) |
| Microsoft. Migrálás | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft. MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Microsoft. NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Virtual Network](../../virtual-network/index.yml)<br />[Virtuális WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft. notebookok | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft. NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft. ObjectStore | Objektum tárolója |
| Microsoft. OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft. OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. peering | [Azure Peering Service](../../peering-service/index.yml) |
| Microsoft. PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. Portal – [regisztrált](#registration) | [Azure Portal](../../azure-portal/index.yml) |
| Microsoft. PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft. PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft. ProjectBabylon | [Azure Data Catalog](../../data-catalog/overview.md) |
| Microsoft. Quantum | [Azure Quantum](https://azure.microsoft.com/services/quantum/) |
| Microsoft. Recoveryservices szolgáltatónál | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft. RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft. Relay | [Azure Relay](../../azure-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph – [regisztrált](#registration) | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft. ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft. Resources – [regisztrált](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. SaaS | Core |
| Microsoft. Scheduler | [Scheduler](../../scheduler/index.yml) |
| Microsoft. Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft. Security | [Biztonsági központ](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| Microsoft. SerialConsole – [regisztrált](#registration) | [Windows rendszerhez készült Azure soros konzol](/troubleshoot/azure/virtual-machines/serial-console-windows) |
| Microsoft.ServiceBus | [Szolgáltatásbusz](/azure/service-bus/) |
| Microsoft. ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft. ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft. Services | Core |
| Microsoft. SignalRService | [Azure SignalR Service](../../azure-signalr/index.yml) |
| Microsoft. SoftwarePlan | Licenc |
| Microsoft. Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../azure-sql/database/index.yml)<br /> [Felügyelt Azure SQL-példány](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. SqlVirtualMachine | [SQL Server az Azure Virtual Machines szolgáltatásban](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft. StorageCache | [Azure HPC Cache](../../hpc-cache/index.yml) |
| Microsoft. StorageSync | [Storage](../../storage/index.yml) |
| Microsoft. StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft. StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft. előfizetés | Core |
| Microsoft. support – [regisztrált](#registration) | Core |
| Microsoft. szinapszis | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft. token | Jogkivonat |
| Microsoft. VirtualMachineImages | [Azure Image Builder](../../virtual-machines/image-builder-overview.md) |
| Microsoft. VisualStudio | [Azure DevOps](/azure/devops/) |
| Microsoft. VMware | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft. VMwareCloudSimple | [Azure VMware Solution by CloudSimple](../../vmware-cloudsimple/index.md) |
| Microsoft. VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft. Web | [APP SERVICE](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft. WindowsDefenderATP | [Microsoft Defender Advanced Threat Protection](../../security-center/security-center-wdatp.md) |
| Microsoft. WindowsESU | Bővített biztonsági frissítések |
| Microsoft. WindowsIoT | [Windows 10 IoT Core Services](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft. WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="registration"></a>Regisztráció

Az előfizetéséhez tartozó, **regisztráltként** megjelölt erőforrások szolgáltatói alapértelmezés szerint regisztrálva vannak. A többi erőforrás-szolgáltató használatához [regisztrálnia kell őket](resource-providers-and-types.md). Azonban számos erőforrás-szolgáltató regisztrálva van, amikor bizonyos műveleteket végez. Ha például létrehoz egy erőforrást a portálon keresztül, a portál automatikusan regisztrálja a szükséges nem regisztrált erőforrás-szolgáltatókat. Ha [Azure Resource Manager sablonnal](../templates/overview.md)telepít erőforrásokat, a szükséges erőforrás-szolgáltatók is regisztrálva lesznek.

> [!IMPORTANT]
> Csak akkor regisztrálja az erőforrás-szolgáltatót, amikor készen áll a használatra. A regisztrációs lépés lehetővé teszi a legalacsonyabb jogosultságok fenntartását az előfizetésen belül. Egy rosszindulatú felhasználó nem használhat olyan erőforrás-szolgáltatót, amely nincs regisztrálva.

## <a name="find-resource-provider"></a>Erőforrás-szolgáltató keresése

Ha meglévő infrastruktúrája van az Azure-ban, de nem biztos benne, hogy melyik erőforrás-szolgáltatót használja, az Azure CLI vagy a PowerShell használatával megkeresheti az erőforrás-szolgáltatót. Adja meg a keresett erőforrásokat tartalmazó erőforráscsoport nevét.

Az alábbi példa az Azure CLI-t használja:

```azurecli-interactive
az resource list -g examplegroup
```

Az eredmények közé tartozik az erőforrástípus. Az erőforrás-szolgáltató névtere az erőforrástípus első része. A következő példa a **Microsoft.** kulcstartó erőforrás-szolgáltatót mutatja be.

```json
[
  {
    ...
    "type": "Microsoft.KeyVault/vaults"
  }
]
```

A következő példa a PowerShellt használja:

```azurepowershell-interactive
Get-AzResource -ResourceGroupName examplegroup
```

Az eredmények közé tartozik az erőforrástípus. Az erőforrás-szolgáltató névtere az erőforrástípus első része. A következő példa a **Microsoft.** kulcstartó erőforrás-szolgáltatót mutatja be.

```azurepowershell
Name              : examplekey
ResourceGroupName : examplegroup
ResourceType      : Microsoft.KeyVault/vaults
...
```

## <a name="next-steps"></a>Következő lépések

Az erőforrás-szolgáltatókkal kapcsolatos további információkért, beleértve az erőforrás-szolgáltatók regisztrálásának módját, tekintse meg az [Azure erőforrás-szolgáltatók és típusok](resource-providers-and-types.md)című témakört.