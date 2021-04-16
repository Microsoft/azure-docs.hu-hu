---
title: Törlés Teljes mód esetén
description: Bemutatja, hogyan kezelik az erőforrástípusok a teljes módú törlést Azure Resource Manager sablonokban.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 6986f600274beaaa67f2f6ce64cbc3d0ceaf322e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576062"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Azure-erőforrások törlése teljes módú üzembe helyezések esetén

Ez a cikk azt ismerteti, hogy az erőforrástípusok hogyan kezelik a törlést, ha nem teljes módban üzembe helyezett sablonban vannak.

Az Igen **jelű** erőforrástípusok törlődnek, ha a típus nincs a teljes módban üzembe helyezett sablonban.

A Nem **jelölésű** erőforrástípusok nem törlődnek automatikusan, ha nincsenek a sablonban; A szülőerőforrás törlésekor azonban törlődnek. A viselkedés teljes leírását az üzembe helyezési módok Azure Resource Manager [lásd:](deployment-modes.md).

Ha egy sablon több erőforráscsoportja számára telepít, [akkor az](./deploy-to-resource-group.md)üzembe helyezési műveletben megadott erőforráscsoport erőforrásai törölhetők. A másodlagos erőforráscsoportok erőforrásai nem törlődnek.

Az erőforrások erőforrás-szolgáltatói névtér szerint vannak felsorolva. Az erőforrás-szolgáltatói névtér és az Azure-szolgáltatás nevének egyeztetését lásd: Erőforrás-szolgáltatók [Azure-szolgáltatásokhoz.](../management/azure-services-resource-providers.md)

> [!NOTE]
> A sablon teljes módban való üzembe helyezése előtt mindig használja a [what-if](template-deploy-what-if.md) műveletet. A What-if azt mutatja meg, hogy mely erőforrások lesznek létrehozva, törölve vagy módosítva. A what-if használatával elkerülheti az erőforrások akaratlan törlését.

Ugrás egy erőforrás-szolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Fog](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Fog](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceRendelés](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Tartományi szolgáltatások | Yes |
> | DomainServices / oucontainer | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | supportProviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | aadsupportcases | No |
> | addsservices (szolgáltatások hozzáadása) | No |
> | Ügynökök | No |
> | anonymousapiusers (névtelen felhasználók) | No |
> | konfiguráció | No |
> | naplók | No |
> | jelentések | No |
> | servicehealthmetrics | No |
> | services | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | advisorScore (tanácsadói pontszám) | No |
> | Konfigurációk | No |
> | generateRecommendations | No |
> | metaadatok | No |
> | Ajánlások | No |
> | mellőzései | No |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | farmBeats | Yes |
> | farmBeats / eventGridFilters | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | actionRules (műveletszabály) | Yes |
> | riasztások | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | migrateFromSmartDetection | No |
> | resourceHealthAlertRules | Yes |
> | smartDetectorAlertRules | Yes |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Szerverek | Yes |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Klaszterek | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | deletedServices | No |
> | getDomainOwnershipIdentifier | No |
> | reportFeedback | No |
> | szolgáltatás | Yes |
> | validateServiceName (szolgáltatásnév érvényesítése) | No |

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | migrateProjects | Yes |
> | migrateProjects /assessments | No |
> | migrateProjects / assessments / assessedApplications | No |
> | migrateProjects / assessments / assessedApplications / machines | No |
> | migrateProjects / assessments / assessedMachines | No |
> | migrateProjects / assessments / assessedMachines / applications | No |
> | migrateProjects / assessments / machinesToAssess | No |
> | migrateProjects / sites | No |
> | migrateProjects / sites / applianceConfigurations | No |
> | migrateProjects / sites / machines | No |
> | osVersions | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | configurationStores | Yes |
> | configurationStores / eventGridFilters | No |
> | configurationStores / keyValues | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Spring | Yes |
> | Spring/apps | No |
> | Spring/ alkalmazások / üzemelő példányok | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | attestationProviders (igazolásiproviderek) | Yes |
> | defaultProviders (alapértelmezettproviderek) | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | No |
> | accessReviewScheduleSettings | No |
> | klasszikusAdminisztrátorok | No |
> | dataAliases (adataliases) | No |
> | dataPolicyManifests | No |
> | denyAssignments (hozzárendelés megtagadása) | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
> | Zárak | No |
> | engedélyek | No |
> | policyAssignments | No |
> | policyDefinitions (szabályzatdefiníciók) | No |
> | policyExemptions (szabályzat-találások) | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | Yes |
> | roleAssignmentApprovals | No |
> | roleAssignments | No |
> | roleAssignmentScheduleInstances | No |
> | roleAssignmentScheduleRequests | No |
> | roleAssignmentSchedules | No |
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions (szerepkördefiníciók) | No |
> | roleEligibilityScheduleInstances | No |
> | roleEligibilityScheduleRequests | No |
> | roleEligibilitySchedules | No |
> | roleManagementPolicies | No |
> | roleManagementPolicyAssignments | No |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | configurationProfileAssignments | No |
> | configurationProfilePreferences | Yes |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | automationAccounts | Yes |
> | automationAccounts /configurations | Yes |
> | automationAccounts /jobs | No |
> | automationAccounts / privateEndpointConnectionProxies | No |
> | automationAccounts / privateEndpointConnections | No |
> | automationAccounts / privateLinkResources | No |
> | automationAccounts/runbookok | Yes |
> | automationAccounts / softwareUpdateConfigurations | No |
> | automationAccounts/webhooks | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | privateClouds | Yes |
> | privateClouds /bővítmények | No |
> | privateClouds /authorizations | No |
> | privateClouds /cloudLinks | No |
> | privateClouds/fürtök | No |
> | privateClouds/clusters /datastores | No |
> | privateClouds / globalReachConnections | No |
> | privateClouds / hcxEnterpriseSites | No |
> | privateClouds /scriptExecutions | No |
> | privateClouds / scriptPackages | No |
> | privateClouds / scriptPackages / scriptCmdlets | No |
> | privateClouds/workloadNetworks | No |
> | privateClouds / workloadNetworks / dhcpConfigurations | No |
> | privateClouds / workloadNetworks / dnsServices | No |
> | privateClouds / workloadNetworks / dnsZones | No |
> | privateClouds /workloadNetworks /gateways | No |
> | privateClouds / workloadNetworks / portMirroringProfiles | No |
> | privateClouds /workloadNetworks /segments | No |
> | privateClouds / workloadNetworks / virtualMachines | No |
> | privateClouds / workloadNetworks / vmGroups | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Környezetben | No |
> | környezetek/fiókok | No |
> | környezetek / fiókok / névterek | No |
> | környezetek / fiókok / névterek / konfigurációk | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | b2cDirectories (b2cDirectories) | Yes |
> | b2ctenants | No |
> | guestUsages | Yes |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dataControllers (dataControllers) | Yes |
> | dataWarehouseInstances | Yes |
> | postgresInstances | Yes |
> | sqlManagedInstances | Yes |
> | sqlServerInstances | Yes |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | AutopilotEnvironments (Autopilot-környezetek) | Yes |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | sqlServerRegistrations (sqlServer-regisztrációk) | Yes |
> | sqlServerRegistrations / sqlServers | No |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Katalógusok | Yes |
> | katalógusok /termékek | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions (edgeSubscriptions) | Yes |
> | linkedSubscriptions (linkedSubscriptions) | Yes |
> | Regisztrációk | Yes |
> | regisztrációk / customerSubscriptions | No |
> | regisztrációk /termékek | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Klaszterek | Yes |
> | galleryImages | Yes |
> | networkInterfaces | Yes |
> | virtualHardDisks | Yes |
> | virtualMachines | Yes |
> | virtualNetworks | Yes |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | bareMetalInstances | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | batchAccounts (kötegek száma) | Yes |
> | batchAccounts / tanúsítványok | No |
> | batchAccounts /pools | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | billingAccounts (számlázási fiókok) | No |
> | billingAccounts /agreements | No |
> | billingAccounts / billingPermissions | No |
> | billingAccounts / billingProfiles | No |
> | billingAccounts / billingProfiles / billingPermissions | No |
> | billingAccounts / billingProfiles / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / billingSubscriptions | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No |
> | billingAccounts / billingProfiles / customers | No |
> | billingAccounts / billingProfiles / instructions | No |
> | billingAccounts / billingProfiles /invoices | No |
> | billingAccounts / billingProfiles / invoices / pricesheet | No |
> | billingAccounts / billingProfiles / számlák / tranzakciók | No |
> | billingAccounts / billingProfiles / invoiceSections | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No |
> | billingAccounts / billingProfiles / invoiceSections / products | No |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | No |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | No |
> | billingAccounts / billingProfiles / invoiceSections / transactions | No |
> | billingAccounts / billingProfiles / invoiceSections /transfers | No |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | No |
> | billingAccounts / BillingProfiles / patchOperations | No |
> | billingAccounts / billingProfiles / paymentMethods | No |
> | billingAccounts / billingProfiles / policies | No |
> | billingAccounts / billingProfiles / pricesheet | No |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | No |
> | billingAccounts / billingProfiles / products | No |
> | billingAccounts / billingProfiles / reservations | No |
> | billingAccounts / billingProfiles / transactions | No |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | No |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | No |
> | billingAccounts / billingRoleAssignments | No |
> | billingAccounts / billingRoleDefinitions | No |
> | billingAccounts / billingSubscriptions | No |
> | billingAccounts / billingSubscriptions / elevateRole | No |
> | billingAccounts / billingSubscriptions / invoices | No |
> | billingAccounts / createBillingRoleAssignment | No |
> | billingAccounts / createInvoiceSectionOperations | No |
> | billingAccounts /customers | No |
> | billingAccounts / customers / billingPermissions | No |
> | billingAccounts / customers / billingSubscriptions | No |
> | billingAccounts / customers / initiateTransfer | No |
> | billingAccounts / customers /policies | No |
> | billingAccounts / customers /products | No |
> | billingAccounts / customers /transactions | No |
> | billingAccounts / customers /transfers | No |
> | billingAccounts /departments | No |
> | billingAccounts / departments / billingPermissions | No |
> | billingAccounts / departments / billingRoleAssignments | No |
> | billingAccounts / departments / billingRoleDefinitions | No |
> | billingAccounts / departments / billingSubscriptions | No |
> | billingAccounts / enrollmentAccounts | No |
> | billingAccounts / enrollmentAccounts / billingPermissions | No |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | No |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | No |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | No |
> | billingAccounts /invoices | No |
> | billingAccounts / invoices / transactions | No |
> | billingAccounts / invoices / transactionSummary | No |
> | billingAccounts / invoiceSections | No |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | No |
> | billingAccounts / invoiceSections / billingSubscriptions | No |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | No |
> | billingAccounts / invoiceSections / elevate | No |
> | billingAccounts / invoiceSections / initiateTransfer | No |
> | billingAccounts / invoiceSections / patchOperations | No |
> | billingAccounts / invoiceSections / productMoveOperations | No |
> | billingAccounts / invoiceSections / products | No |
> | billingAccounts / invoiceSections / products / transfer | No |
> | billingAccounts / invoiceSections / products / updateAutoRenew | No |
> | billingAccounts / invoiceSections / transactions | No |
> | billingAccounts / invoiceSections /transfers | No |
> | billingAccounts / lineOfCredit | No |
> | billingAccounts / patchOperations | No |
> | billingAccounts / payableOverage | No |
> | billingAccounts / paymentMethods | No |
> | billingAccounts / payNow | No |
> | billingAccounts /products | No |
> | billingAccounts /reservations | No |
> | billingAccounts / transactions | No |
> | billingPeriods (számlázási időszak) | No |
> | billingPermissions | No |
> | billingProperty (számlázástulajdonság) | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | Szervezeti egységek | No |
> | enrollmentAccounts | No |
> | Számlák | No |
> | Promóciók | No |
> | Transzferek | No |
> | transfers / acceptTransfer | No |
> | transfers /declineTransfer | No |
> | transfers /operationStatus | No |
> | transfers / validateTransfer | No |
> | validateAddress (cím ellenőrzése) | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | mapApis | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | cordaMembers | Yes |
> | Watchers | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | TokenServices | Yes |
> | TokenServices / BlockchainNetworks | No |
> | TokenServices / Groups | No |
> | TokenServices / Groups / Accounts | No |
> | TokenServices / TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments / assignmentOperations | No |
> | blueprintAssignments / operations | No |
> | Tervrajz | No |
> | tervek /összetevők | No |
> | tervek/verziók | No |
> | blueprints / versions / artifacts | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | botServices | Yes |
> | botServices /channels | No |
> | botServices /connections | No |
> | hostSettings | No |
> | nyelv | No |
> | sablonok | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Redis | Yes |
> | Redis / EventGridFilters | No |
> | Redis / privateEndpointConnectionProxies | No |
> | Redis / privateEndpointConnectionProxies / validate | No |
> | Redis / privateEndpointConnections | No |
> | Redis/privateLinkResources | No |
> | redisEnterprise (redisEnterprise) | Yes |
> | redisEnterprise / adatbázisok | No |
> | RedisEnterprise / privateEndpointConnectionProxies | No |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | No |
> | RedisEnterprise / privateEndpointConnections | No |
> | RedisEnterprise / privateLinkResources | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appliedReservations | No |
> | autoQuotaIncrease | No |
> | calculateExchange | No |
> | calculatePrice (ár kiszámítása) | No |
> | calculatePurchasePrice | No |
> | Katalógusok | No |
> | commercialReservationOrders | No |
> | Exchange | No |
> | ownReservations (saját lefoglalások) | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders / calculateRefund | No |
> | reservationOrders / merge | No |
> | reservationOrders /reservations | No |
> | reservationOrders / reservations /revisions | No |
> | reservationOrders /return | No |
> | reservationOrders /split | No |
> | reservationOrders /swap | No |
> | Foglalás | No |
> | resourceProviders (erőforrás-providers) | No |
> | resources | No |
> | validateReservationOrder | No |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Helyek | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Yes |
> | élcsomópont | No |
> | Profilok | Yes |
> | profilok /afdendpoints | Yes |
> | profilok / afdendpoints / útvonalak | No |
> | profilok /egyénitartományok | No |
> | profilok/végpontok | Yes |
> | profilok/ végpontok / egyénitartományok | No |
> | profilok/ végpontok /forráscsoportok | No |
> | profilok/ végpontok /eredetek | No |
> | profiles /origingroups | No |
> | profiles / origingroups / origins | No |
> | profilok/szabálykészletek | No |
> | profilok / szabálykészletek / szabályok | No |
> | profilok /titkos kulcsok | No |
> | profilok /biztonsági irányelvei | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | certificateOrders (tanúsítványrendelések) | Yes |
> | certificateOrders /certificates | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Változások | No |
> | profil | No |
> | resourceChanges (erőforrás-váltások) | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | képességek | No |
> | domainNames (tartománynevek) | Yes |
> | tartománynevek / képességek | No |
> | domainNames / internalLoadBalancers | No |
> | domainNames / serviceCertificates | No |
> | tartománynevek /tárolóhelyek | No |
> | tartománynevek / tárolóhelyek / szerepkörök | No |
> | domainNames / slots / roles / metricDefinitions | No |
> | tartománynevek / tárolóhelyek / szerepkörök / metrikák | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | kvóták | No |
> | resourceTypes (erőforrástípusok) | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Yes |
> | virtualMachines / diagnosticSettings | No |
> | virtualMachines / metricDefinitions | No |
> | virtualMachines / metrics | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | képességek | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections /társviszonyok | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Yes |
> | kvóták | No |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks / virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | képességek | No |
> | Lemezek | No |
> | images | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | kvóták | No |
> | storageAccounts (tárfiókok) | Yes |
> | storageAccounts / blobServices | No |
> | storageAccounts / fileServices | No |
> | storageAccounts / metricDefinitions | No |
> | storageAccounts / metrics | No |
> | storageAccounts / queueServices | No |
> | storageAccounts / services | No |
> | storageAccounts / services / diagnosticSettings | No |
> | storageAccounts / services / metricDefinitions | No |
> | storageAccounts / services / metrics | No |
> | storageAccounts / tableServices | No |
> | storageAccounts / vmImages | No |
> | vmImages | No |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Csomópontok | Yes |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Tervek | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | accounts / privateEndpointConnectionProxies | No |
> | accounts / privateEndpointConnections | No |
> | accounts /privateLinkResources | No |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates (Aggr. használattal) | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | availabilitySets (rendelkezésre állási készlet) | Yes |
> | cloudServices | Yes |
> | cloudServices / networkInterfaces | No |
> | cloudServices / publicIPAddresses | No |
> | cloudServices /roleInstances | No |
> | cloudServices / roleInstances / networkInterfaces | No |
> | cloudServices / roles | No |
> | diskAccesses | Yes |
> | diskEncryptionSets | Yes |
> | Lemezek | Yes |
> | Galériák | Yes |
> | katalógusok /alkalmazások | No |
> | katalógusok / alkalmazások / verziók | No |
> | katalógusok /képek | Yes |
> | katalógusok / képek / verziók | Yes |
> | hostGroups (gazdagépcsoportok) | Yes |
> | hostGroups /hosts | Yes |
> | images | Yes |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections / restorePoints | No |
> | sharedVMExtensions (megosztottVM-extensions) | Yes |
> | sharedVMExtensions / verziók | No |
> | sharedVMImages | Yes |
> | sharedVMImages / verziók | No |
> | pillanatképek | Yes |
> | sshPublicKeys | Yes |
> | virtualMachines | Yes |
> | virtualMachines /extensions | Yes |
> | virtualMachines / metricDefinitions | No |
> | virtualMachines / runCommands | Yes |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets /extensions | No |
> | virtualMachineScaleSets / networkInterfaces | No |
> | virtualMachineScaleSets / publicIPAddresses | No |
> | virtualMachineScaleSets / virtualMachines | No |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | No |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Gyorsítótár-csomópontok | Yes |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | platformAccounts | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | ResourcePools (Erőforráskészletek) | Yes |
> | VCenterek | Yes |
> | VCenters / InventoryItems | No |
> | VirtualMachines | Yes |
> | VirtualMachines / Bővítmények | Yes |
> | VirtualMachines / GuestAgents | No |
> | VirtualMachines / HybridIdentityMetadata | No |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | AggregatedCost (Összesített költség) | No |
> | Egyenlegek | No |
> | Költségvetések | No |
> | Díjak | No |
> | Költségcímkék | No |
> | Hitelek | No |
> | események | No |
> | Előrejelzések | No |
> | Sok | No |
> | Piacokon | No |
> | Árlisták | No |
> | Termékek | No |
> | ReservationDetails (Foglalási részletek) | No |
> | ReservationRecommendationDetails | No |
> | ReservationRecommendations (Foglalásirecommendations) | No |
> | ReservationSummaries (Foglalási fájlok) | No |
> | ReservationTransactions (Foglalások tranzakciói) | Nem |
> | Címkék | Nem |
> | Bérlők | No |
> | Kifejezések | No |
> | UsageDetails | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | containerGroups | Yes |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Nyilvántartások | Yes |
> | beállításregisztrálók /agentPools | Yes |
> | beállításregisztrálók /buildek | No |
> | beállításregisztrálók / buildek / megszakítás | No |
> | regisztrációs naplók / buildek / getLogLink | No |
> | beállításregisztrálók / buildTasks | Yes |
> | beállításregisztrálók / buildTasks / lépések | No |
> | beállításregisztrációk /connectedRegistries | No |
> | beállításregisztrációk / connectedRegistries / inaktiválás | No |
> | beállításregisztrálók / eventGridFilters | No |
> | beállításregisztrálók / exportPipeline-ek | No |
> | beállításregisztrálók / generateCredentials | No |
> | registries / getBuildSourceUploadUrl | No |
> | beállításregisztrálók / GetCredentials | No |
> | beállítás-nyilvántartások / importImage | No |
> | beállításregisztrálók / importPipeline-ek | No |
> | beállításregisztrálók /pipelineRuns | No |
> | registries / privateEndpointConnectionProxies | No |
> | registries / privateEndpointConnectionProxies / validate | No |
> | beállításregisztrálók / privateEndpointConnections | No |
> | beállításregisztrálók / privateLinkResources | No |
> | beállításregisztrálók / queueBuild | No |
> | registries / regenerateCredential | No |
> | beállításregisztrálók / regenerateCredentials | No |
> | beállításregisztrációs jegyzékek /replikációk | Yes |
> | beállításregisztrálók /futtatás | No |
> | beállításregisztrálók / futtatás /megszakítás | No |
> | beállításregisztrálók / scheduleRun | No |
> | beállításregisztrálók / scopeMaps | No |
> | beállításregisztrálók /taskRuns | No |
> | beállításregisztrálók /feladatok | Yes |
> | beállításregisztrálók / jogkivonatok | No |
> | beállításregisztrálók / updatePolicies | No |
> | beállításregisztrálók /webhookok | Yes |
> | beállításregisztrálók / webhookok / getCallbackConfig | No |
> | beállításregisztrálók / webhookok / ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | containerServices | Yes |
> | managedClusters | Yes |
> | ManagedClusters / eventGridFilters | No |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Riasztások | No |
> | BillingAccounts (Számlázási fiókok) | No |
> | Költségvetések | No |
> | CloudConnectors (Felhőcsatlakozók) | No |
> | Összekötők | Yes |
> | costAllocationRules | No |
> | Részlegek | No |
> | Dimenziók | No |
> | EnrollmentAccounts (Regisztráció-fiókok) | No |
> | Exportálások | No |
> | ExternalBillingAccounts (Külső fiókok száma) | No |
> | ExternalBillingAccounts / Riasztások | No |
> | ExternalBillingAccounts / Dimensions | No |
> | ExternalBillingAccounts / Forecast | No |
> | ExternalBillingAccounts / Query | No |
> | ExternalSubscriptions (Külső előírók) | No |
> | ExternalSubscriptions / Riasztások | No |
> | ExternalSubscriptions / Dimensions | No |
> | ExternalSubscriptions / Forecast | No |
> | ExternalSubscriptions / Query | No |
> | fetchPrices (beolvasási ár) | No |
> | Előrejelzés | No |
> | GenerateDetailedCostReport | No |
> | GenerateReservationDetailsReport | No |
> | Insights | No |
> | Lekérdezés | No |
> | Regisztráció | No |
> | Jelentéskonfigák | No |
> | Jelentések | No |
> | ScheduledActions (Ütemezett osztások) | No |
> | Beállítások | No |
> | showbackRules (visszacsatolásiszabály) | No |
> | Megjelenítések | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DisableLockbox (ALockbox letiltása) | No |
> | EnableLockbox | No |
> | Kérelmek | No |
> | TenantOptedIn | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Egyesületek | No |
> | resourceProviders | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Példányok | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Munkahelyek | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DataBoxEdgeDevices (DataBoxEdgeDevices) | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | munkaterületek | Yes |
> | munkaterületek / dbWorkspaces | No |
> | munkaterületek / virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Katalógusok | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dataFactories (DataFactories) | Yes |
> | dataFactories / diagnosticSettings | No |
> | dataFactories / metricDefinitions | No |
> | dataFactorySchema | No |
> | Gyárak | Yes |
> | gyárak /integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | accounts / dataLakeStoreAccounts | No |
> | accounts /storageAccounts | No |
> | accounts / storageAccounts /containers | No |
> | accounts / transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | accounts / eventGridFilters | No |
> | accounts /firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DatabaseMigrations (Adatbázis-migrálások) | No |
> | services | Yes |
> | szolgáltatások/projektek | Yes |
> | SqlMigrationServices | Yes |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | BackupVaults | Yes |
> | ResourceGuards | Yes |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | fiókok/megosztások | No |
> | fiókok/ megosztások / adatkészletek | No |
> | fiókok / megosztások / meghívók | No |
> | fiókok / megosztások / szolgáltatómegosztások előiratai | No |
> | fiókok / megosztások / synchronizationSettings | No |
> | fiókok/megosztások előiratai | No |
> | accounts / sharesubscriptions / consumerSourceDataSets | No |
> | accounts / sharesubscriptions / datasetmappings | No |
> | fiókok / megosztások előiratai / eseményindítók | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Szerverek | Yes |
> | servers /advisors | No |
> | kiszolgálók /kulcsok | No |
> | servers / privateEndpointConnectionProxies | No |
> | servers /privateEndpointConnections | No |
> | servers /privateLinkResources | No |
> | servers /queryTexts | No |
> | servers / recoverableServers | No |
> | servers / resetQueryPerformanceInsightData | No |
> | servers /start | No |
> | servers /stop | No |
> | servers /topQueryStatistics | No |
> | servers /virtualNetworkRules | No |
> | servers /waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | flexibleServers (rugalmas kiszolgáló) | Yes |
> | Szerverek | Yes |
> | servers /advisors | No |
> | kiszolgálók/kulcsok | No |
> | servers / privateEndpointConnectionProxies | No |
> | servers /privateEndpointConnections | No |
> | servers /privateLinkResources | No |
> | servers /queryTexts | No |
> | servers / recoverableServers | No |
> | servers / resetQueryPerformanceInsightData | No |
> | servers /start | No |
> | servers /stop | No |
> | servers /topQueryStatistics | No |
> | servers /upgrade | No |
> | servers /virtualNetworkRules | No |
> | servers /waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | serverGroups | Yes |
> | serverGroupsv2 | Yes |
> | Szerverek | Yes |
> | kiszolgálók/tanácsadók | No |
> | kiszolgálók/kulcsok | No |
> | servers / privateEndpointConnectionProxies | No |
> | servers /privateEndpointConnections | No |
> | servers /privateLinkResources | No |
> | servers /queryTexts | No |
> | servers / recoverableServers | No |
> | servers / resetQueryPerformanceInsightData | No |
> | servers /topQueryStatistics | No |
> | servers /virtualNetworkRules | No |
> | servers /waitStatistics | No |
> | serversv2 | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | artifactSources | Yes |
> | bevezetések | Yes |
> | serviceTopologies (szolgáltatástopológiák) | Yes |
> | serviceTopologies / services | Yes |
> | serviceTopologies / services / serviceUnits | Yes |
> | lépések | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazáscsoportok | Yes |
> | alkalmazáscsoportok / alkalmazások | No |
> | alkalmazáscsoportok /asztali számítógépek | No |
> | applicationgroups / startmenuitems | No |
> | gazdagépkészletek | Yes |
> | hostpools / msixpackages | No |
> | hostpools /sessionhosts | No |
> | hostpools / sessionhosts / usersessions | No |
> | hostpools / usersessions | No |
> | scalingPlans (méretezésisíkok) | Yes |
> | munkaterületek | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | ElasticPools | Yes |
> | ElasticPools / IotHubTenants | Yes |
> | ElasticPools / IotHubTenants / securitySettings | No |
> | IotHubs | Yes |
> | IotHubs / eventGridFilters | No |
> | IotHubs / securitySettings | No |
> | ProvisioningServices | Yes |
> | Használat | No |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | fiókok /példányok | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Csővezetékek | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Vezérlők | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | labcenters | Yes |
> | Labs | Yes |
> | tesztkörnyezetek/környezetek | Yes |
> | labs / serviceRunners | Yes |
> | labs / virtualMachines | Yes |
> | Menetrend | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | digitalTwinsInstances | Yes |
> | digitalTwinsInstances /endpoints | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | cassandraClusters | Yes |
> | databaseAccountNames (adatbázis-fiókok nevei) | No |
> | databaseAccounts (adatbázis-fiókok) | Yes |
> | restorableDatabaseAccounts | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Tartományok | Yes |
> | domains / domainOwnershipIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelTartományok | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects / clouddeployments | No |
> | lcsprojects /connectors | No |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Címek | Yes |
> | orderCollections | Yes |
> | rendelések | Yes |
> | productFamiliesMetadata | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Tartományok | Yes |
> | tartományok/témakörök | No |
> | eventSubscriptions (eventSubscriptions) | No |
> | extensionTopics (bővítménytopikák) | No |
> | partnerNamespaces | Yes |
> | partnerNamespaces / eventChannels | No |
> | partnerRegistrations | Yes |
> | partnertopics | Yes |
> | partnertopics / eventSubscriptions | No |
> | systemTopics | Yes |
> | systemTopics / eventSubscriptions | No |
> | Témák | Yes |
> | topicTypes (témakörtípusok) | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Klaszterek | Yes |
> | Névterek | Yes |
> | névterek /engedélyezési szabályok | No |
> | névterek / disasterrecoveryconfigs | No |
> | névterek / eventhubs | No |
> | névterek / eventhubs /authorizationrules | No |
> | névterek / eventhubs / consumergroups | No |
> | névterek /networkrulesets | No |
> | névterek / privateEndpointConnections | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | experimentWorkspaces | Yes |

## <a name="microsoftfalcon"></a>Microsoft.Fog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Névterek | Yes |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | featureConfigurations (funkciókonfigurációk) | No |
> | featureProviderNamespaces | No |
> | featureProviders | No |
> | funkciók | No |
> | Szolgáltatók | No |
> | subscriptionFeatureRegistrations | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Beiratkozik | No |
> | galleryitems (gyűjtemények) | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas / területek | No |
> | myareas / területek /területek | No |
> | myareas / területek / területek / katalógusok | No |
> | myareas / területek / katalógusok | No |
> | myareas / galleryitems | No |
> | Regisztráció | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | autoManagedAccounts | Yes |
> | autoManagedVmConfigurationProfiles | Yes |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | Szoftver | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitors | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dedicatedHSMs | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | clusterPools (fürtkészletek) | Yes |
> | fürtkészletek/fürtök | Yes |
> | Klaszterek | Yes |
> | fürtök /alkalmazások | No |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | healthBots | Yes |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Yes |
> | szolgáltatások / iomtconnectors | No |
> | szolgáltatások / iomtconnectors / kapcsolatok | No |
> | szolgáltatások / iomtconnectors / leképezések | No |
> | services / privateEndpointConnectionProxies | No |
> | services / privateEndpointConnections | No |
> | services / privateLinkResources | No |
> | munkaterületek | Yes |
> | munkaterületek / dicomservices | Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Gépek | Yes |
> | gépek /ki értékelt kizárások | No |
> | gépek/bővítmények | Yes |
> | machines /installPatches | No |
> | machines / privateLinkScopes | No |
> | privateLinkScopes | Yes |
> | privateLinkScopes / privateEndpointConnectionProxies | No |
> | privateLinkScopes / privateEndpointConnections | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dataManagers | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | eszközök | Yes |
> | networkfunctions | Yes |
> | networkFunctionVendors | No |
> | registeredSubscriptions | No |
> | Szállítók | No |
> | Szállítók/szállítók | No |
> | Beszállítók/szállítók / előzetes verziókelőirat | No |
> | virtualNetworkFunctions | Yes |
> | virtualNetworkFunctionVendors | No |

## <a name="microsofthydra"></a>Microsoft.Fog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Összetevők | Yes |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Munkahelyek | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appTemplates (appTemplates) | No |
> | IoTApps | Yes |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | defenderSettings | No |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Graph | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | deletedManagedHSMs | No |
> | töröltvaults | No |
> | hsmPools | Yes |
> | managedHSMs | Yes |
> | Boltívek | Yes |
> | tárolók / accessPolicies | No |
> | tárolók / eventGridFilters | No |
> | tárolók/kulcsok | No |
> | tárolók/ kulcsok / verziók | No |
> | tárolók /titkos kulcsok | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | connectedClusters (csatlakoztatott fürtök) | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kiterjesztés | No |
> | sourceControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Klaszterek | Yes |
> | fürtök /attacheddatabaseconfigurations | No |
> | fürtök/adatbázisok | No |
> | fürtök / adatbázisok / adatkapcsolatok | No |
> | fürtök / adatbázisok / eventhubconnections | No |
> | fürtök / adatbázisok / principalassignments | No |
> | fürtök / adatbázisok / szkriptek | No |
> | fürtök /adatkapcsolatok | No |
> | fürtök / principalassignments | No |
> | fürtök/megosztott entitások | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | labplans | Yes |
> | Labs | Yes |
> | felhasználók | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | integrationAccounts (integrationAccounts) | Yes |
> | integrationServiceKörnyezetek | Yes |
> | integrationServiceEnvironments / managedApis | Yes |
> | isolatedEnvironments (elkülönített környezetek) | Yes |
> | Munkafolyamatok | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | commitmentPlans (kötelezettségvállalásisíkok) | Yes |
> | webServices | Yes |
> | Munkaterületek | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | modelinventories (modellinventories) | Yes |
> | virtualclusters (virtuális fürtök) | Yes |
> | munkaterületek | Yes |
> | munkaterületek / batchEndpoints | Yes |
> | munkaterületek / batchEndpoints / üzemelő példányok | Yes |
> | munkaterületek / batchEndpoints / üzemelő példányok / feladatok | No |
> | munkaterületek / batchEndpoints / feladatok | No |
> | munkaterületek/kódok | No |
> | munkaterületek / kódok / verziók | No |
> | munkaterületek /számítási erőforrások | No |
> | munkaterületek /adatok | No |
> | munkaterületek/adattárak | No |
> | munkaterületek/környezetek | No |
> | munkaterületek / eventGridFilters | No |
> | munkaterületek /feladatok | No |
> | workspaces /labelingJobs | No |
> | munkaterületek /linkedServices | No |
> | munkaterületek /modellek | No |
> | munkaterületek / modellek / verziók | No |
> | munkaterületek / onlineEndpoints | Yes |
> | munkaterületek / onlineEndpoints / üzemelő példányok | Yes |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | applyUpdates (applyUpdates) | No |
> | configurationAssignments | No |
> | maintenanceConfigurations (karbantartás konfigurálása) | Yes |
> | publicMaintenanceConfigurations | No |
> | Frissítések | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Identitások | No |
> | userAssignedIdentities (userAssignedIdentities) | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managedNetworks | Yes |
> | managedNetworks / managedNetworkGroups | Yes |
> | managedNetworks / managedNetworkPeeringPolicies | Yes |
> | értesítés | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions (registrationDefinitions) | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | getEntities (Entitások lekérte) | No |
> | managementGroups (felügyeleti csoportok) | No |
> | managementGroups / beállítások | No |
> | resources | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | fiókok /létrehozók | Yes |
> | accounts / eventGridFilters | No |
> | accounts /privateAtlases | Yes |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | macc | No |
> | Kínál | No |
> | offerTypes (ajánlattípusok) | No |
> | offerTypes /publishers | No |
> | offerTypes / közzétevők / ajánlatok | No |
> | offerTypes / publishers / offers /plans | No |
> | offerTypes / publishers / offers / plans / agreements | No |
> | offerTypes / publishers / offers / plans / configs | No |
> | offerTypes / publishers / offers / plans / configs / importImage | No |
> | privategalleryitems | No |
> | privateStoreClient | No |
> | privateStores | No |
> | privateStores / AdminRequestApprovals | No |
> | privateStores / ajánlatok | No |
> | privateStores / offers / acknowledgeNotification | No |
> | privateStores / queryNotificationsState | No |
> | privateStores / RequestApprovals | No |
> | privateStores / requestApprovals / query | No |
> | privateStores / requestApprovals / egyedsík | No |
> | Termékek | No |
> | Kiadók | No |
> | közzétevők/ajánlatok | No |
> | közzétevők/ ajánlatok/módosítások | No |
> | Regisztráció | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicDevServices | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceRendelés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Megállapodások | No |
> | offertypes (ajánlattípusok) | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | mediaservices | Yes |
> | mediaservices / accountFilters | No |
> | mediaservices /assets | No |
> | mediaservices / assets / assetFilters | No |
> | mediaservices / contentKeyPolicies | No |
> | mediaservices / eventGridFilters | No |
> | mediaservices / graphInstances | No |
> | mediaservices / graphTopologies | No |
> | mediaservices / liveEventOperations | No |
> | mediaservices / liveEvents | Yes |
> | mediaservices / liveEvents / liveOutputs | No |
> | mediaservices /liveOutputOperations | No |
> | mediaservices / mediaGraphs | No |
> | mediaservices / privateEndpointConnectionOperations | No |
> | mediaservices / privateEndpointConnectionProxies | No |
> | mediaservices / privateEndpointConnections | No |
> | mediaservices / streamingEndpointOperations | No |
> | mediaservices / streamingEndpoints | Yes |
> | mediaservices /streamingLocators | No |
> | mediaservices / streamingPolicies | No |
> | mediaservices /transforms | No |
> | mediaservices / transforms / jobs | No |
> | videoAnalyzers | Yes |
> | videoAnalyzers / edgeModules | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appClusters | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | assessmentProjects | Yes |
> | migrateprojects | Yes |
> | moveCollections | Yes |
> | Projektek | Yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectAnchorsAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | Yes |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Hálózatok | Yes |
> | networks /sites | Yes |
> | packetCores (csomagmagok) | Yes |
> | Sims | Yes |
> | sims / simProfiles | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | netAppAccounts (netAppAccounts) | Yes |
> | netAppAccounts / accountBackups | No |
> | netAppAccounts / capacityPools | Yes |
> | netAppAccounts / capacityPools /volumes | Yes |
> | netAppAccounts / capacityPools / kötetek / pillanatképek | No |
> | netAppAccounts / volumeGroups | No |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | applicationGateways | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Yes |
> | bastionHosts | Yes |
> | bgpServiceCommunities (bgpServiceCommunities) | No |
> | Kapcsolatok | Yes |
> | ddosCustomPolicies | Yes |
> | ddosProtectionPlans | Yes |
> | dnsOperationStatuses | No |
> | dnszones | Yes |
> | dnszones / A | No |
> | dnszones / AAAA | No |
> | dnszones / all | No |
> | dnszones / CAA | No |
> | dnszones / CNAME | No |
> | dnszones / MX | No |
> | dnszones / NS | No |
> | dnszones / PTR | No |
> | dnszones / rekordkészletek | No |
> | dnszones / SOA | No |
> | dnszones / SRV | No |
> | dnszones / TXT | No |
> | expressRouteCircuits | Yes |
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Yes |
> | frontdoors (frontdoors) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | No |
> | internalNotify (belső megjegyzés) | No |
> | ipGroups (IP-csoportok) | Yes |
> | loadBalancers | Yes |
> | localNetworkGateways | Yes |
> | natGateways | Yes |
> | networkIntentPolicies | Yes |
> | networkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers /connectionMonitors | Yes |
> | networkWatchers / flowLogs | Yes |
> | networkWatchers /lenses | Yes |
> | networkWatchers / pingMeshes | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Yes |
> | privateDnsZones / A | No |
> | privateDnsZones / AAAA | No |
> | privateDnsZones / all | No |
> | privateDnsZones / CNAME | No |
> | privateDnsZones / MX | No |
> | privateDnsZones / PTR | No |
> | privateDnsZones / SOA | No |
> | privateDnsZones / SRV | No |
> | privateDnsZones / TXT | No |
> | privateDnsZones / virtualNetworkLinks | Yes |
> | privateEndpoints (privateEndpoints) | Yes |
> | privateLinkServices | Yes |
> | publicIPAddresses | Yes |
> | publicIPPrefixes | Yes |
> | routeFilters (útvonalszűrők) | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles / heatMaps | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks /alhálózatok | No |
> | virtualNetworkTaps | Yes |
> | virtualWans | Yes |
> | vpnGateways | Yes |
> | vpnSites (vpnSites) | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | NotebookProxies | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Névterek | Yes |
> | névterek / notificationHubs | Yes |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | osNamespaces (osNamespaces) | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | HyperVSites (HiperVSites) | Yes |
> | ImportSites (Importálási beállítások) | Yes |
> | Főkiszolgálók | Yes |
> | Kiszolgálóiwebhelyek | Yes |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Klaszterek | Yes |
> | deletedWorkspaces | No |
> | linkTargets | No |
> | querypacks | Yes |
> | storageInsightConfigs | No |
> | munkaterületek | Yes |
> | munkaterületek /adatokExportálások | No |
> | munkaterületek / adatforrások | No |
> | munkaterületek / linkedServices | No |
> | workspaces / linkedStorageAccounts | No |
> | munkaterületek / metaadatok | No |
> | munkaterületek / lekérdezés | No |
> | workspaces / scopedPrivateLinkProxies | No |
> | munkaterületek / storageInsightConfigs | No |
> | munkaterületek /táblák | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | felügyelet társításai | No |
> | managementconfigurations (felügyeleti konfigurációk) | Yes |
> | megoldások | Yes |
> | Kilátás nyílik | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | No |
> | legacyPeerings | No |
> | peerAsns | No |
> | társviszonyok | Yes |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | társviszony-létesítésSzolgáltatások | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | attestations (attestations) | No |
> | eventGridFilters | No |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | szervizelések | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> |  -konzolok | No |
> | irányítópultok | Yes |
> | tenantconfigurations (bérlőkonfigurációk) | No |
> | UserSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Yes |
> | Bérlők | Yes |
> | bérlők/munkaterületek | No |
> | workspaceCollections (munkaterület-collections) | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | automatikus skálázásVCores | Yes |
> | Kapacitások | Yes |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | enterprisePolicies | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | deletedAccounts (Fiókok törlése) | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | providerRegistrations (szolgáltatói regisztrációk) | No |
> | providerRegistrations / customRollouts | No |
> | providerRegistrations / defaultRollouts | No |
> | providerRegistrations / resourceTypeRegistrations | No |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | deletedAccounts (Fiókok törlése) | No |
> | getDefaultAccount (GetDefaultAccount) | No |
> | removeDefaultAccount | No |
> | setDefaultAccount (SetDefaultAccount) | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Munkaterületek | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | Boltívek | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | OpenShiftClusters | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Névterek | Yes |
> | névterek /engedélyezési szabályok | No |
> | névterek /hibrid kapcsolat | No |
> | névterek / hibrid összekapcsolások / engedélyezési szabályok | No |
> | névterek / privateEndpointConnections | No |
> | névterek / wcfrelays | No |
> | névterek / wcfrelays / authorizationrules | No |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Készülékek | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Lekérdezések | Yes |
> | resourceChangeDetails | No |
> | resourceChanges (erőforrás-váltások) | No |
> | resources | No |
> | resourcesHistory | No |
> | subscriptionsStatus | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | availabilityStatuses (rendelkezésre állásiállapotok) | No |
> | childAvailabilityStatuses | No |
> | childResources | No |
> | emergingissues | No |
> | események | No |
> | impactedResources | No |
> | metaadatok | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Telepítések | No |
> | üzemelő példányok/műveletek | No |
> | deploymentScripts | Yes |
> | deploymentScripts / naplók | No |
> | Linkek | No |
> | Szolgáltatók | No |
> | resourceGroups (erőforráscsoportok) | No |
> | Előfizetések | No |
> | templateSpecs | Yes |
> | templateSpecs /versions | Yes |
> | Bérlők | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazások | Yes |
> | resources | Yes |
> | saasresources | No |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Felhők | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |
> | vmmservers | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | riasztások | No |
> | alertsSuppressionRules | No |
> | allowedConnections (engedélyezett kapcsolat) | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | értékelések | No |
> | autoDismissAlertsRules | No |
> | automatizálások | Yes |
> | AutoProvisioningSettings | No |
> | Megfelelőség | No |
> | összekötők | No |
> | dataCollectionAgents | No |
> | eszközök | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | ingestionSettings | No |
> | Betekintést | No |
> | iotAlerts (iotAlerts) | No |
> | iotAlertTypes | No |
> | iotDefenderSettings | No |
> | iotRecommendations (iotRecommendations) | No |
> | iotRecommendationTypes | No |
> | iotSecuritySolutions | Yes |
> | iotSecuritySolutions / analyticsModels | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No |
> | iotSecuritySolutions / iotAlerts | No |
> | iotSecuritySolutions / iotAlertTypes | No |
> | iotSecuritySolutions / iotRecommendations | No |
> | iotSecuritySolutions / iotRecommendationTypes | No |
> | iotSensors (iotSensors) | No |
> | iotSites (iotSites) | No |
> | jitNetworkAccessPolicies | No |
> | jitPolicies | No |
> | onPremiseIotSensors | No |
> | policies | No |
> | díjszabás | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores (secureScores) | No |
> | secureScores / secureScoreControls | No |
> | securityContacts | No |
> | biztonságMegoldások | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | beállítások | No |
> | sqlVulnerabilityAssessments | No |
> | alértékelések | No |
> | feladatok | No |
> | Topológiák | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Összesítései | No |
> | alertRules | No |
> | alertRuleTemplates | No |
> | automationRules | No |
> | könyvjelzők | No |
> | Esetekben | No |
> | dataConnectors (adatcsatlakozók) | No |
> | dataConnectorsCheckRequirements | No |
> | Gazdagodás | No |
> | Entitások | No |
> | entityQueries (entitáslekérdezés) | No |
> | entityQueryTemplates (entityQueryTemplates) | No |
> | incidensek | No |
> | officeConsents (officeConsents) | No |
> | beállítások | No |
> | threatIntelligence (fenyegetésintelligencia) | No |
> | megnézőlisták | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | konzolSzolgáltatások | No |
> | serialPorts (serialPorts) | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Névterek | Yes |
> | névterek / engedélyezési szabályok | No |
> | névterek / disasterrecoveryconfigs | No |
> | névterek / eventgridfilters | No |
> | névterek /networkrulesets | No |
> | névterek / privateEndpointConnections | No |
> | névterek /üzenetsorok | No |
> | névterek / üzenetsorok / engedélyezési szabályok | No |
> | névterek /témakörök | No |
> | névterek / témakörök / engedélyezési szabályok | No |
> | névterek / témakörök / előfizetések | No |
> | névterek / témakörök / előfizetések / szabályok | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazások | Yes |
> | Klaszterek | Yes |
> | fürtök /alkalmazások | No |
> | containerGroups | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters / applications | No |
> | managedclusters (felügyelt fürtök) | Yes |
> | managedclusters / alkalmazások | No |
> | managedclusters / alkalmazások /szolgáltatások | No |
> | managedclusters / applicationTypes | No |
> | managedclusters / applicationTypes / versions | No |
> | managedclusters / nodetypes | No |
> | Hálózatok | Yes |
> | titkos kulcstárolók | Yes |
> | titkos kulcstárolók /tanúsítványok | No |
> | titkos kulcstitkok/titkos kulcsok | No |
> | volumes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazások | Yes |
> | containerGroups | Yes |
> | Átjárók | Yes |
> | Hálózatok | Yes |
> | Titkok | Yes |
> | volumes | Yes |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Linkerek | No |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | providerRegistrations (szolgáltatói regisztrációk) | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | bevezetések | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | SignalR | Yes |
> | SignalR/ eventGridFilters | No |
> | WebPubSub | Yes |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | accounts /accountQuotaPolicies | No |
> | accounts / groupPolicies | No |
> | fiókok /feladatok | No |
> | fiókok / storageContainers | No |
> | images | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | applicationDefinitions (alkalmazásdefiníciók) | Yes |
> | alkalmazások | Yes |
> | jitRequests | Yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances /databases | Yes |
> | managedInstances / databases / backupShortTermRetentionPolicies | No |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | No |
> | managedInstances / databases / vulnerabilityAssessments | No |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | No |
> | managedInstances / encryptionProtector | No |
> | managedInstances / keys | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No |
> | managedInstances / vulnerabilityAssessments | No |
> | Szerverek | Yes |
> | kiszolgálók/rendszergazdák | No |
> | servers /communicationLinks | No |
> | kiszolgálók/adatbázisok | Yes |
> | servers /encryptionProtector | No |
> | servers /firewallRules | No |
> | kiszolgálók/kulcsok | No |
> | servers / restorableDroppedDatabases | No |
> | servers /serviceobjectives | No |
> | servers / tdeCertificates | No |
> | virtualClusters (virtuális fürtök) | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | deletedAccounts (Fiókok törlése) | No |
> | storageAccounts (tárfiókok) | Yes |
> | storageAccounts / blobServices | No |
> | storageAccounts / fileServices | No |
> | storageAccounts / queueServices | No |
> | storageAccounts / services | No |
> | storageAccounts / services / metricDefinitions | No |
> | storageAccounts / tableServices | No |
> | Használat | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | amlFilesystems | Yes |
> | Elrejt | Yes |
> | gyorsítótárak / storageTargets | No |
> | usageModels (használatmodellek) | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | replicationGroups | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices / munkafolyamatok | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices /workflows | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices /workflows | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Vezetők | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Klaszterek | Yes |
> | fürtök / privateEndpoints | No |
> | streamingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | acceptChangeTenant | No |
> | acceptOwnership (tulajdonos elfogadása) | No |
> | acceptOwnershipStatus | No |
> | Aliasok | No |
> | mégse | No |
> | changeTenantRequest | No |
> | changeTenantStatus | No |
> | CreateSubscription (Előíró létrehozása) | No |
> | Engedélyezi | No |
> | policies | No |
> | átnevezés | No |
> | SubscriptionDefinitions (Előfizetés-definíciók) | No |
> | SubscriptionOperations (Előfizetési együttműködések) | No |
> | Előfizetések | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | privateLinkHubs | Yes |
> | munkaterületek | Yes |
> | workspaces / bigDataPools | Yes |
> | workspaces / operationStatuses | No |
> | workspaces / sqlDatabases | Yes |
> | workspaces / sqlPools | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Környezetben | Yes |
> | környezetek / accessPolicies | No |
> | környezetek /eseményforrások | Yes |
> | környezetek / privateEndpointConnectionProxies | No |
> | környezetek / privateEndpointConnections | No |
> | környezetek / privateLinkResources | No |
> | környezetek / referenceDataSets | Yes |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Üzletek | Yes |
> | stores / accessPolicies | No |
> | áruházak/szolgáltatások | No |
> | stores / services / tokens | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | imageTemplates | Yes |
> | imageTemplates / runOutputs | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | ArcZones | Yes |
> | ResourcePools (Erőforráskészletek) | Yes |
> | VCenterek | Yes |
> | VCenters / InventoryItems | No |
> | virtualmachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dedicatedCloudNodes (dedikált felhőcsomópont) | Yes |
> | dedicatedCloudServices | Yes |
> | virtualMachines | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | eszközök | Yes |
> | registeredSubscriptions | No |
> | Szállítók | No |
> | szállítók/termékkódok | No |
> | szállítók /vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | vnfs | Yes |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Yes |
> | Tervek | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts / apiAcls | No |
> | apiManagementAccounts / apis | No |
> | apiManagementAccounts / apis / apiAcls | No |
> | apiManagementAccounts / apis / connectionAcls | No |
> | apiManagementAccounts / apis /connections | No |
> | apiManagementAccounts / apis / connections / connectionAcls | No |
> | apiManagementAccounts / apis / localizedDefinitions | No |
> | apiManagementAccounts / connectionAcls | No |
> | apiManagementAccounts /connections | No |
> | billingMeters (számlázási mérők) | No |
> | tanúsítványok | Yes |
> | connectionGateways | Yes |
> | Kapcsolatok | Yes |
> | customApis (egyéniapis) | Yes |
> | deletedSites (Törölt webhelyek) | No |
> | functionAppStacks | No |
> | generateGithubAccessTokenForAppserviceCLI | No |
> | hostingEnvironments | Yes |
> | hostingEnvironments / eventGridFilters | No |
> | hostingEnvironments / multiRolePools | No |
> | hostingEnvironments / workerPools | No |
> | kubeEnvironments | Yes |
> | közzétételHasználók | No |
> | Ajánlások | No |
> | resourceHealthMetadata | No |
> | runtimes (futtatás) | No |
> | serverFarms | Yes |
> | serverFarms / eventGridFilters | No |
> | serverFarms / firstPartyApps | No |
> | serverFarms / firstPartyApps / keyVaultSettings | No |
> | Helyek | Yes |
> | helyek/konfiguráció  | No |
> | sites / eventGridFilters | No |
> | sites / hostNameBindings | No |
> | sites /networkConfig | No |
> | sites /premieraddons | Yes |
> | helyek/tárolóhelyek | Yes |
> | sites / slots / eventGridFilters | No |
> | sites / slots / hostNameBindings | No |
> | helyek/ tárolóhelyek /networkConfig | No |
> | sourceControls (forrásvezérlők) | No |
> | staticSites (statikus webhelyek) | Yes |
> | Érvényesít | No |
> | verifyHostingEnvironmentVnet | No |
> | webAppStacks | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | multipleActivationKeys | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DeviceServices (Eszközszolgáltatások) | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | migrationAgents | Yes |
> | Munkaterhek | Yes |
> | számítási feladatok /példányok | No |
> | számítási feladatok /verziók | No |
> | számítási feladatok/ verziók / összetevők | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Monitorok | Nem |

## <a name="next-steps"></a>Következő lépések

Ha vesszővel elválasztott értékeket is tartalmaz, töltse le az [complete-mode-deletion.csv. ](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)
