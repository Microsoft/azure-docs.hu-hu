---
title: Címkék támogatása erőforrásokhoz
description: Megjeleníti, hogy mely Azure-erőforrástípusok támogatják a címkéket. Az összes Azure-szolgáltatás részleteit tartalmazza.
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b196cae267a8d7dc878f055f6b2d70a3ff6f9313
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773962"
---
# <a name="tag-support-for-azure-resources"></a>Címketámogatás Azure-erőforrásokhoz
Ez a cikk azt ismerteti, hogy egy erőforrástípus támogatja-e a [címkéket.](tag-resources.md) A Címkék **támogatottak oszlop** jelzi, hogy az erőforrástípus rendelkezik-e a címke tulajdonságával. A Költségjelentés **címkével** jelölt Címke oszlop jelzi, hogy az erőforrástípus átadja-e a címkét a költségjelentésnek. A költségeket címkék alapján is megtekintheti a Cost Management [költségelemzésben,](../../cost-management-billing/costs/group-filter.md) valamint az Azure számlázási és napi [használati adataiban.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Ha vesszővel elválasztott értékeket is tartalmaz, töltse le az [tag-support.csv. ](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)

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
> - [Microsoft.Insights](#microsoftinsights)
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
> - [Microsoft.MarketplaceAlkalmazások](#microsoftmarketplaceapps)
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
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | DomainServices (Tartományi szolgáltatások) | Igen | Yes |
> | DomainServices / oucontainer | Nem | Nem |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nem | Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nem | Nem |
> | addsservices | Nem | Nem |
> | Ügynökök | Nem | Nem |
> | anonymousapiusers (névtelen felhasználók) | Nem | Nem |
> | konfiguráció | Nem | Nem |
> | naplók | Nem | Nem |
> | jelentések | Nem | Nem |
> | servicehealthmetrics (szolgáltatás-egészségmetriák) | Nem | Nem |
> | services | Nem | Nem |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | advisorScore (advisorScore) | Nem | Nem |
> | Konfigurációk | Nem | Nem |
> | generateRecommendations | Nem | Nem |
> | metaadatok | Nem | Nem |
> | Ajánlások | Nem | Nem |
> | mellőzései | Nem | Nem |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | farmBeats | Igen | Yes |
> | farmBeats / eventGridFilters | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | actionRules (műveletszabály) | Igen | Yes |
> | riasztások | Nem | Nem |
> | alertsList | Nem | Nem |
> | alertsMetaData | Nem | Nem |
> | riasztásokSummary | Nem | Nem |
> | alertsSummaryList | Nem | Nem |
> | migrateFromSmartDetection | Nem | Nem |
> | resourceHealthAlertRules | Igen | Yes |
> | smartDetectorAlertRules | Igen | Yes |
> | smartGroups | Nem | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Szerverek | Igen | Yes |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Klaszterek | Igen | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | deletedServices | Nem | Nem |
> | getDomainOwnershipIdentifier | Nem | Nem |
> | reportFeedback | Nem | Nem |
> | szolgáltatás | Igen | Yes |
> | validateServiceName (Szolgáltatásnév érvényesítése) | Nem | Nem |

> [!NOTE]
> Az Azure API Management szolgáltatás csak legfeljebb 15 címkenév/érték pár létrehozását támogatja az egyes szolgáltatásokhoz.

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | migrateProjects | Igen | Yes |
> | migrateProjects /assessments | Nem | Nem |
> | migrateProjects / assessments / assessedApplications | Nem | Nem |
> | migrateProjects / assessments / assessedApplications / machines | Nem | Nem |
> | migrateProjects / assessments / assessedMachines | Nem | Nem |
> | migrateProjects / assessments / assessedMachines / applications | Nem | Nem |
> | migrateProjects / assessments / machinesToAssess | Nem | Nem |
> | migrateProjects / sites | Nem | Nem |
> | migrateProjects / sites / applianceConfigurations | Nem | Nem |
> | migrateProjects / sites / machines | Nem | Nem |
> | osVersions | Nem | Nem |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | configurationStores | Igen | Nem |
> | configurationStores / eventGridFilters | Nem | Nem |
> | configurationStores / keyValues | Nem | Nem |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Spring | Igen | Yes |
> | Spring / alkalmazások | Nem | Nem |
> | Spring / alkalmazások / üzemelő példányok | Nem | Nem |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | attestationProviders (igazolásiproviderek) | Igen | Yes |
> | defaultProviders (alapértelmezettproviderek) | Nem | Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | Nem | Nem |
> | accessReviewScheduleSettings | Nem | Nem |
> | klasszikusAdminisztrátorok | Nem | Nem |
> | dataAliases (adataliasok) | Nem | Nem |
> | dataPolicyManifests | Nem | Nem |
> | denyAssignments (hozzárendelés megtagadása) | Nem | Nem |
> | elevateAccess | Nem | Nem |
> | findOrphanRoleAssignments | Nem | Nem |
> | Zárak | Nem | Nem |
> | engedélyek | Nem | Nem |
> | policyAssignments | Nem | Nem |
> | policyDefinitions (szabályzatdefiníciók) | Nem | Nem |
> | policyExemptions (szabályzattitkok) | Nem | Nem |
> | policySetDefinitions | Nem | Nem |
> | privateLinkAssociations | Nem | Nem |
> | providerOperations (szolgáltatói együttműködések) | Nem | Nem |
> | resourceManagementPrivateLinks | Igen | Yes |
> | roleAssignmentApprovals | Nem | Nem |
> | roleAssignments (szerepkör hozzárendelései) | Nem | Nem |
> | roleAssignmentScheduleInstances | Nem | Nem |
> | roleAssignmentScheduleRequests | Nem | Nem |
> | roleAssignmentSchedules | Nem | Nem |
> | roleAssignmentsUsageMetrics | Nem | Nem |
> | roleDefinitions (szerepkördefiníciók) | Nem | Nem |
> | roleEligibilityScheduleInstances | Nem | Nem |
> | roleEligibilityScheduleRequests | Nem | Nem |
> | roleEligibilitySchedules | Nem | Nem |
> | roleManagementPolicies | Nem | Nem |
> | roleManagementPolicyAssignments | Nem | Nem |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | configurationProfileAssignments | Nem | Nem |
> | configurationProfilePreferences | Igen | Yes |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Igen | Yes |
> | automationAccounts /configurations | Igen | Yes |
> | automationAccounts /jobs | Nem | Nem |
> | automationAccounts / privateEndpointConnectionProxies | Nem | Nem |
> | automationAccounts / privateEndpointConnections | Nem | Nem |
> | automationAccounts / privateLinkResources | Nem | Nem |
> | automationAccounts /runbookok | Igen | Yes |
> | automationAccounts / softwareUpdateConfigurations | Nem | Nem |
> | automationAccounts /webhooks | Nem | Nem |

> [!NOTE]
> Azure Automation automation-erőforrásokhoz csak legfeljebb 15 címkenév/érték pár létrehozását támogatja.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | privateClouds | Igen | Yes |
> | privateClouds /bővítmények | Nem | Nem |
> | privateClouds /authorizations | Nem | Nem |
> | privateClouds /cloudLinks | Nem | Nem |
> | privateClouds/fürtök | Nem | Nem |
> | privateClouds/clusters /datastores | Nem | Nem |
> | privateClouds / globalReachConnections | Nem | Nem |
> | privateClouds / hcxEnterpriseSites | Nem | Nem |
> | privateClouds /scriptExecutions | Nem | Nem |
> | privateClouds /scriptPackages | Nem | Nem |
> | privateClouds / scriptPackages / scriptCmdlets | Nem | Nem |
> | privateClouds/workloadNetworks | Nem | Nem |
> | privateClouds / workloadNetworks / dhcpConfigurations | Nem | Nem |
> | privateClouds / workloadNetworks / dnsServices | Nem | Nem |
> | privateClouds / workloadNetworks / dnsZones | Nem | Nem |
> | privateClouds/workloadNetworks /gateways | Nem | Nem |
> | privateClouds / workloadNetworks / portMirroringProfiles | Nem | Nem |
> | privateClouds/workloadNetworks /segments | Nem | Nem |
> | privateClouds / workloadNetworks / virtualMachines | Nem | Nem |
> | privateClouds / workloadNetworks / vmGroups | Nem | Nem |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Környezetben | Nem | Nem |
> | környezetek /fiókok | Nem | Nem |
> | környezetek / fiókok / névterek | Nem | Nem |
> | környezetek / fiókok / névterek / konfigurációk | Nem | Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | b2cDirectories (b2cDirectories) | Igen | Nem |
> | b2ctenants | Nem | Nem |
> | guestUsages | Igen | Yes |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | dataControllers (dataControllers) | Igen | Yes |
> | dataWarehouseInstances | Igen | Yes |
> | postgresInstances | Igen | Yes |
> | sqlManagedInstances | Igen | Yes |
> | sqlServerInstances | Igen | Yes |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | AutopilotEnvironments (Autopilot-környezetek) | Igen | Yes |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | sqlServerRegistrations (SqlServer-regisztrációk) | Igen | Yes |
> | sqlServerRegistrations / sqlServers | Nem | Nem |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Katalógusok | Igen | Yes |
> | katalógusok /termékek | Igen | Yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Nem | Nem |
> | edgeSubscriptions (edgeSubscriptions) | Igen | Yes |
> | linkedSubscriptions (linkedSubscriptions) | Igen | Yes |
> | Regisztrációk | Igen | Yes |
> | regisztrációk / customerSubscriptions | Nem | Nem |
> | regisztrációk /termékek | Nem | Nem |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Klaszterek | Igen | Yes |
> | galleryImages | Igen | Yes |
> | networkInterfaces | Igen | Yes |
> | virtualHardDisks | Igen | Yes |
> | virtualMachines | Igen | Yes |
> | virtualNetworks | Igen | Yes |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Igen | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | batchAccounts (kötegek száma) | Igen | Yes |
> | batchAccounts / tanúsítványok | Nem | Nem |
> | batchAccounts /pools | Nem | Nem |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nem | Nem |
> | billingAccounts / agreements | Nem | Nem |
> | billingAccounts / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles | Nem | Nem |
> | billingAccounts / billingProfiles / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingProfiles / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / billingProfiles / customers | Nem | Nem |
> | billingAccounts / billingProfiles / instructions | Nem | Nem |
> | billingAccounts / billingProfiles / invoices | Nem | Nem |
> | billingAccounts / billingProfiles / invoices / pricesheet | Nem | Nem |
> | billingAccounts / billingProfiles / számlák / tranzakciók | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections /transfers | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | Nem | Nem |
> | billingAccounts / BillingProfiles / patchOperations | Nem | Nem |
> | billingAccounts / billingProfiles / paymentMethods | Nem | Nem |
> | billingAccounts / billingProfiles / policies | Nem | Nem |
> | billingAccounts / billingProfiles / pricesheet | Nem | Nem |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nem | Nem |
> | billingAccounts / billingProfiles / products | Nem | Nem |
> | billingAccounts / billingProfiles / reservations | Nem | Nem |
> | billingAccounts / billingProfiles / transactions | Nem | Nem |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | Nem | Nem |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Nem | Nem |
> | billingAccounts / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingSubscriptions / elevateRole | Nem | Nem |
> | billingAccounts / billingSubscriptions / invoices | Nem | Nem |
> | billingAccounts / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / createInvoiceSectionOperations | Nem | Nem |
> | billingAccounts /customers | Nem | Nem |
> | billingAccounts / customers / billingPermissions | Nem | Nem |
> | billingAccounts / customers / billingSubscriptions | Nem | Nem |
> | billingAccounts / customers / initiateTransfer | Nem | Nem |
> | billingAccounts / customers /policies | Nem | Nem |
> | billingAccounts / customers /products | Nem | Nem |
> | billingAccounts / customers /transactions | Nem | Nem |
> | billingAccounts / customers /transfers | Nem | Nem |
> | billingAccounts /departments | Nem | Nem |
> | billingAccounts / departments / billingPermissions | Nem | Nem |
> | billingAccounts / departments / billingRoleAssignments | Nem | Nem |
> | billingAccounts / departments / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / departments / billingSubscriptions | Nem | Nem |
> | billingAccounts / enrollmentAccounts | Nem | Nem |
> | billingAccounts / enrollmentAccounts / billingPermissions | Nem | Nem |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Nem | Nem |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | Nem | Nem |
> | billingAccounts /invoices | Nem | Nem |
> | billingAccounts / invoices / transactions | Nem | Nem |
> | billingAccounts / invoices / transactionSummary | Nem | Nem |
> | billingAccounts / invoiceSections | Nem | Nem |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nem | Nem |
> | billingAccounts / invoiceSections / billingSubscriptions | Nem | Nem |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Nem | Nem |
> | billingAccounts / invoiceSections / elevate | Nem | Nem |
> | billingAccounts / invoiceSections / initiateTransfer | Nem | Nem |
> | billingAccounts / invoiceSections / patchOperations | Nem | Nem |
> | billingAccounts / invoiceSections / productMoveOperations | Nem | Nem |
> | billingAccounts / invoiceSections / products | Nem | Nem |
> | billingAccounts / invoiceSections / products / transfer | Nem | Nem |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Nem | Nem |
> | billingAccounts / invoiceSections / transactions | Nem | Nem |
> | billingAccounts / invoiceSections /transfers | Nem | Nem |
> | billingAccounts / lineOfCredit | Nem | Nem |
> | billingAccounts / patchOperations | Nem | Nem |
> | billingAccounts / payableOverage | Nem | Nem |
> | billingAccounts / paymentMethods | Nem | Nem |
> | billingAccounts / payNow | Nem | Nem |
> | billingAccounts /products | Nem | Nem |
> | billingAccounts / reservations | Nem | Nem |
> | billingAccounts / transactions | Nem | Nem |
> | billingPeriods (számlázási időszak) | Nem | Nem |
> | billingPermissions | Nem | Nem |
> | billingProperty (számlázástulajdonság) | Nem | Nem |
> | billingRoleAssignments | Nem | Nem |
> | billingRoleDefinitions | Nem | Nem |
> | createBillingRoleAssignment | Nem | Nem |
> | Szervezeti egységek | Nem | Nem |
> | enrollmentAccounts | Nem | Nem |
> | Számlák | Nem | Nem |
> | Promóciók | Nem | Nem |
> | Transzferek | Nem | Nem |
> | transfers /acceptTransfer | Nem | Nem |
> | transfers/declineTransfer | Nem | Nem |
> | transfers /operationStatus | Nem | Nem |
> | transfers /validateTransfer | Nem | Nem |
> | validateAddress (cím ellenőrzése) | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | mapApis | Igen | Yes |
> | updateCommunicationPreference | Nem | Nem |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Igen | Yes |
> | cordaMembers | Igen | Yes |
> | Watchers | Igen | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | TokenServices | Igen | Yes |
> | TokenServices / BlockchainNetworks | Nem | Nem |
> | TokenServices / Groups | Nem | Nem |
> | TokenServices / Groups / Accounts | Nem | Nem |
> | TokenServices / TokenTemplates | Nem | Nem |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments (terv hozzárendelései) | Nem | Nem |
> | blueprintAssignments / assignmentOperations | Nem | Nem |
> | blueprintAssignments / operations | Nem | Nem |
> | Tervrajz | Nem | Nem |
> | tervek /összetevők | Nem | Nem |
> | tervek/verziók | Nem | Nem |
> | blueprints / versions / artifacts | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | botServices | Igen | Yes |
> | botServices /channels | Nem | Nem |
> | botServices /connections | Nem | Nem |
> | hostSettings | Nem | Nem |
> | nyelv | Nem | Nem |
> | sablonok | Nem | Nem |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Redis | Igen | Yes |
> | Redis / EventGridFilters | Nem | Nem |
> | Redis / privateEndpointConnectionProxies | Nem | Nem |
> | Redis / privateEndpointConnectionProxies / validate | Nem | Nem |
> | Redis / privateEndpointConnections | Nem | Nem |
> | Redis/privateLinkResources | Nem | Nem |
> | redisEnterprise | Igen | Yes |
> | redisEnterprise / adatbázisok | Nem | Nem |
> | RedisEnterprise / privateEndpointConnectionProxies | Nem | Nem |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | Nem | Nem |
> | RedisEnterprise / privateEndpointConnections | Nem | Nem |
> | RedisEnterprise / privateLinkResources | Nem | Nem |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nem | Nem |
> | autoQuotaIncrease | Nem | Nem |
> | calculateExchange | Nem | Nem |
> | calculatePrice (ár kiszámítása) | Nem | Nem |
> | calculatePurchasePrice | Nem | Nem |
> | Katalógusok | Nem | Nem |
> | commercialReservationOrders | Nem | Nem |
> | Exchange | Nem | Nem |
> | ownReservations (saját lefoglalások) | Nem | Nem |
> | placePurchaseOrder | Nem | Nem |
> | reservationOrders (Foglalásrendelések) | Nem | Nem |
> | reservationOrders / calculateRefund | Nem | Nem |
> | reservationOrders / merge | Nem | Nem |
> | reservationOrders /reservations | Nem | Nem |
> | reservationOrders / reservations /revisions | Nem | Nem |
> | reservationOrders / return | Nem | Nem |
> | reservationOrders /split | Nem | Nem |
> | reservationOrders / felcserélés | Nem | Nem |
> | Foglalás | Nem | Nem |
> | resourceProviders (erőforrás-providers) | Nem | Nem |
> | resources | Nem | Nem |
> | validateReservationOrder | Nem | Nem |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Helyek | Igen | Yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nem | Nem |
> | CdnWebApplicationFirewallPolicies | Igen | Yes |
> | élcsomópont | Nem | Nem |
> | Profilok | Igen | Yes |
> | profilok /afdendpoints | Igen | Yes |
> | profiles / afdendpoints / routes | Nem | Nem |
> | profilok /egyénitartományok | Nem | Nem |
> | profilok/végpontok | Igen | Yes |
> | profilok / végpontok / egyénitartományok | Nem | Nem |
> | profilok / végpontok / forráscsoportok | Nem | Nem |
> | profilok/ végpontok / eredetek | Nem | Nem |
> | profiles /origingroups | Nem | Nem |
> | profiles / origingroups /origins | Nem | Nem |
> | profilok/szabálykészletek | Nem | Nem |
> | profilok / szabálykészletek / szabályok | Nem | Nem |
> | profilok /titkos kulcsok | Nem | Nem |
> | profilok /biztonsági irányelvei | Nem | Nem |
> | validateProbe | Nem | Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | certificateOrders (tanúsítványrendelések) | Igen | Yes |
> | certificateOrders /certificates | Nem | Nem |
> | validateCertificateRegistrationInformation | Nem | Nem |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Változások | Nem | Nem |
> | profil | Nem | Nem |
> | resourceChanges (erőforrás-váltások) | Nem | Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | képességek | Nem | Nem |
> | domainNames (tartománynevek) | Nem | Nem |
> | domainNames / capabilities | Nem | Nem |
> | domainNames / internalLoadBalancers | Nem | Nem |
> | domainNames / serviceCertificates | Nem | Nem |
> | tartománynevek /tárolóhelyek | Nem | Nem |
> | tartománynevek / tárolóhelyek / szerepkörök | Nem | Nem |
> | domainNames / slots / roles / metricDefinitions | Nem | Nem |
> | tartománynevek / tárolóhelyek / szerepkörök / metrikák | Nem | Nem |
> | moveSubscriptionResources | Nem | Nem |
> | operatingSystemFamilies | Nem | Nem |
> | operatingSystems | Nem | Nem |
> | kvóták | Nem | Nem |
> | resourceTypes (erőforrástípusok) | Nem | Nem |
> | validateSubscriptionMoveAvailability | Nem | Nem |
> | virtualMachines | Nem | Nem |
> | virtualMachines / diagnosticSettings | Nem | Nem |
> | virtualMachines / metricDefinitions | Nem | Nem |
> | virtualMachines / metrikák | Nem | Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nem | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | képességek | Nem | Nem |
> | expressRouteCrossConnections | Nem | Nem |
> | expressRouteCrossConnections /társviszonyok | Nem | Nem |
> | gatewaySupportedDevices | Nem | Nem |
> | networkSecurityGroups | Nem | Nem |
> | kvóták | Nem | Nem |
> | reservedIps | Nem | Nem |
> | virtualNetworks | Nem | Nem |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nem | Nem |
> | virtualNetworks / virtualNetworkPeerings | Nem | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | képességek | Nem | Nem |
> | Lemezek | Nem | Nem |
> | images | Nem | Nem |
> | osImages | Nem | Nem |
> | osPlatformImages | Nem | Nem |
> | publicImages | Nem | Nem |
> | kvóták | Nem | Nem |
> | storageAccounts (tárfiókok) | Nem | Nem |
> | storageAccounts / blobServices | Nem | Nem |
> | storageAccounts / fileServices | Nem | Nem |
> | storageAccounts / metricDefinitions | Nem | Nem |
> | storageAccounts / metrics | Nem | Nem |
> | storageAccounts / queueServices | Nem | Nem |
> | storageAccounts / services | Nem | Nem |
> | storageAccounts / services / diagnosticSettings | Nem | Nem |
> | storageAccounts / services / metricDefinitions | Nem | Nem |
> | storageAccounts / services / metrics | Nem | Nem |
> | storageAccounts / tableServices | Nem | Nem |
> | storageAccounts / vmImages | Nem | Nem |
> | vmImages | Nem | Nem |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Csomópontok | Igen | Yes |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Tervek | Igen | Nem |
> | registeredSubscriptions | Nem | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | accounts / privateEndpointConnectionProxies | Nem | Nem |
> | accounts / privateEndpointConnections | Nem | Nem |
> | accounts /privateLinkResources | Nem | Nem |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | RateCard | Nem | Nem |
> | UsageAggregates (Aggr. használat) | Nem | Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | availabilitySets (rendelkezésre állási készlet) | Igen | Yes |
> | cloudServices | Igen | Yes |
> | cloudServices / networkInterfaces | Nem | Nem |
> | cloudServices / publicIPAddresses | Nem | Nem |
> | cloudServices / roleInstances | Nem | Nem |
> | cloudServices / roleInstances / networkInterfaces | Nem | Nem |
> | cloudServices / roles | Nem | Nem |
> | diskAccesses | Igen | Yes |
> | diskEncryptionSets | Igen | Yes |
> | Lemezek | Igen | Yes |
> | Galériák | Igen | Yes |
> | katalógusok /alkalmazások | Nem | Nem |
> | katalógusok / alkalmazások /verziók | Nem | Nem |
> | katalógusok /képek | Nem | Nem |
> | katalógusok / képek / verziók | Nem | Nem |
> | hostGroups (gazdagépcsoportok) | Igen | Yes |
> | hostGroups /hosts | Igen | Yes |
> | images | Igen | Yes |
> | proximityPlacementGroups | Igen | Yes |
> | restorePointCollections | Igen | Yes |
> | restorePointCollections / restorePoints | Nem | Nem |
> | sharedVMExtensions (megosztottVM-extensions) | Igen | Yes |
> | sharedVMExtensions / verziók | Nem | Nem |
> | sharedVMImages | Igen | Yes |
> | sharedVMImages / verziók | Nem | Nem |
> | pillanatképek | Igen | Yes |
> | sshPublicKeys | Igen | Yes |
> | virtualMachines | Igen | Yes |
> | virtualMachines /extensions | Igen | Yes |
> | virtualMachines / metricDefinitions | Nem | Nem |
> | virtualMachines / runCommands | Igen | Yes |
> | virtualMachineScaleSets | Igen | Yes |
> | virtualMachineScaleSets /extensions | Nem | Nem |
> | virtualMachineScaleSets / networkInterfaces | Nem | Nem |
> | virtualMachineScaleSets / publicIPAddresses | Igen | Nem |
> | virtualMachineScaleSets / virtualMachines | Nem | Nem |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nem | Nem |

> [!NOTE]
> Általánosítottként megjelölt virtuális géphez nem adhat hozzá címkét. A virtuális gép általánosítottként való megjelölése a [Set-AzVm -Generalized vagy](/powershell/module/Az.Compute/Set-AzVM) [az az vm generalize fájlban található.](/cli/azure/vm#az_vm_generalize)

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Gyorsítótár-csomópontok | Igen | Yes |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | platformAccounts | Igen | Yes |
> | registeredSubscriptions | Nem | Nem |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | ResourcePools (Erőforráskészletek) | Igen | Yes |
> | VCenterek | Igen | Yes |
> | VCenters / InventoryItems | Nem | Nem |
> | VirtualMachines | Igen | Yes |
> | VirtualMachines / Bővítmények | Igen | Yes |
> | VirtualMachines / GuestAgents | Nem | Nem |
> | VirtualMachines / HybridIdentityMetadata | Nem | Nem |
> | VirtualMachineTemplates | Igen | Yes |
> | VirtualNetworks | Igen | Yes |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | AggregatedCost (Összesített költség) | Nem | Nem |
> | Egyenlegek | Nem | Nem |
> | Költségvetések | Nem | Nem |
> | Díjak | Nem | Nem |
> | Költségcímkék | Nem | Nem |
> | Hitelek | Nem | Nem |
> | események | Nem | Nem |
> | Előrejelzések | Nem | Nem |
> | Sok | Nem | Nem |
> | Piacokon | Nem | Nem |
> | Árlisták | Nem | Nem |
> | Termékek | Nem | Nem |
> | ReservationDetails (Foglalási részletek) | Nem | Nem |
> | ReservationRecommendationDetails | Nem | Nem |
> | ReservationRecommendations (Foglalásirecommendations) | Nem | Nem |
> | ReservationSummaries (Foglalási fájlok) | Nem | Nem |
> | ReservationTransactions (Foglalások tranzakciói) | Nem | Nem |
> | Címkék | Nem | Nem |
> | Bérlők | Nem | Nem |
> | Kifejezések | Nem | Nem |
> | UsageDetails | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | containerGroups | Igen | Yes |
> | serviceAssociationLinks | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Nyilvántartások | Igen | Yes |
> | beállításregisztrálók /agentPools | Igen | Yes |
> | beállításregisztrálók /buildek | Nem | Nem |
> | beállításregisztrálók / buildek / megszakítás | Nem | Nem |
> | beállításregisztrálók / buildek / getLogLink | Nem | Nem |
> | beállításregisztrálók / buildTasks | Igen | Yes |
> | beállításregisztrálók / buildTasks / lépések | Nem | Nem |
> | beállításregisztrációk /connectedRegistries | Nem | Nem |
> | beállításregisztrációk / connectedRegistries / inaktiválás | Nem | Nem |
> | beállításregisztrálók / eventGridFilters | Nem | Nem |
> | beállításregisztrálók / exportPipeline-ek | Nem | Nem |
> | beállításregisztrálók / generateCredentials | Nem | Nem |
> | beállításregisztrálók / getBuildSourceUploadUrl | Nem | Nem |
> | beállításregisztrálók / GetCredentials | Nem | Nem |
> | beállításregisztrátorok / importImage | Nem | Nem |
> | beállításregisztrálók / importPipeline-fájlok | Nem | Nem |
> | beállításregisztrálók / folyamatokFuttatók | Nem | Nem |
> | beállításregisztrációk / privateEndpointConnectionProxies | Nem | Nem |
> | beállításregisztrációk / privateEndpointConnectionProxies / validate | Nem | Nem |
> | beállításregisztrálók / privateEndpointConnections | Nem | Nem |
> | beállításregisztrálók / privateLinkResources | Nem | Nem |
> | beállításregisztrálók / queueBuild | Nem | Nem |
> | beállításregisztrálók / regenerateCredential | Nem | Nem |
> | beállításregisztrálók / regenerateCredentials | Nem | Nem |
> | beállításregisztrációs jegyzékek /replikációk | Igen | Yes |
> | beállításregisztrálók /futtatás | Nem | Nem |
> | beállításregisztrálók / futtatás /megszakítás | Nem | Nem |
> | beállításregisztrálók / scheduleRun | Nem | Nem |
> | beállításregisztrálók / scopeMaps | Nem | Nem |
> | beállításregisztrálók /taskRuns | Nem | Nem |
> | beállításregisztrálók /feladatok | Igen | Yes |
> | beállításregisztrálók / jogkivonatok | Nem | Nem |
> | beállításregisztrálók / updatePolicies | Nem | Nem |
> | beállításregisztrálók /webhookok | Igen | Yes |
> | beállításregisztrálók / webhookok / getCallbackConfig | Nem | Nem |
> | beállításregisztrálók / webhookok / ping | Nem | Nem |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | containerServices | Igen | Yes |
> | managedClusters (felügyelt fürtök) | Igen | Yes |
> | ManagedClusters / eventGridFilters | Nem | Nem |
> | openShiftManagedClusters | Igen | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Riasztások | Nem | Nem |
> | BillingAccounts (Számlázási fiókok) | Nem | Nem |
> | Költségvetések | Nem | Nem |
> | CloudConnectors (Felhőcsatlakozók) | Nem | Nem |
> | Összekötők | Igen | Yes |
> | costAllocationRules | Nem | Nem |
> | Részlegek | Nem | Nem |
> | Dimenziók | Nem | Nem |
> | EnrollmentAccounts (Regisztráció-fiókok) | Nem | Nem |
> | Exportálások | Nem | Nem |
> | ExternalBillingAccounts (Külső fiókok száma) | Nem | Nem |
> | ExternalBillingAccounts / Riasztások | Nem | Nem |
> | ExternalBillingAccounts / Dimensions | Nem | Nem |
> | ExternalBillingAccounts / Forecast | Nem | Nem |
> | ExternalBillingAccounts / Query | Nem | Nem |
> | ExternalSubscriptions (Külső előirat) | Nem | Nem |
> | ExternalSubscriptions / Riasztások | Nem | Nem |
> | ExternalSubscriptions / Dimensions | Nem | Nem |
> | ExternalSubscriptions / Előrejelzés | Nem | Nem |
> | ExternalSubscriptions / Query | Nem | Nem |
> | fetchPrices (beolvasási ár) | Nem | Nem |
> | Előrejelzés | Nem | Nem |
> | GenerateDetailedCostReport | Nem | Nem |
> | GenerateReservationDetailsReport | Nem | Nem |
> | Insights | Nem | Nem |
> | Lekérdezés | Nem | Nem |
> | Regisztráció | Nem | Nem |
> | Jelentéskonfigs | Nem | Nem |
> | Jelentések | Nem | Nem |
> | ScheduledActions (Ütemezettactions) | Nem | Nem |
> | Beállítások | Nem | Nem |
> | showbackRules (visszacsatolásiszabály) | Nem | Nem |
> | Megjelenítések | Nem | Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | DisableLockbox (ALockbox letiltása) | Nem | Nem |
> | EnableLockbox | Nem | Nem |
> | Kérelmek | Nem | Nem |
> | TenantOptedIn | Nem | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Egyesületek | Nem | Nem |
> | resourceProviders (erőforrás-providers) | Igen | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Példányok | Igen | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Munkahelyek | Igen | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices (DataBoxEdgeDevices) | Igen | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | munkaterületek | Igen | Yes |
> | munkaterületek / dbMunkaterek | Nem | Nem |
> | munkaterületek / virtualNetworkPeerings | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Katalógusok | Igen | Yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | dataFactories (DataFactories) | Igen | Yes |
> | dataFactories / diagnosticSettings | Nem | Nem |
> | dataFactories / metricDefinitions | Nem | Nem |
> | dataFactorySchema | Nem | Nem |
> | Gyárak | Igen | Yes |
> | gyárak /integrationRuntimes | Nem | Nem |

> [!NOTE]
> Ha azure-SSIS integrációs futtatókörnyezetekkel dolgozik az adat-előállítóban, a futtatás költségei data factory-címkékkel lesznek megcímkézve. Az Azure-SSIS integrációs futtatókörnyezetek futtatását le kell állítani és újra kell indítani ahhoz, hogy az új data factory-címkéket a futtatás költségeikre alkalmazva legyen.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | accounts / dataLakeStoreAccounts | Nem | Nem |
> | accounts /storageAccounts | Nem | Nem |
> | accounts / storageAccounts /containers | Nem | Nem |
> | accounts / transferAnalyticsUnits | Nem | Nem |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | accounts / eventGridFilters | Nem | Nem |
> | accounts /firewallRules | Nem | Nem |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | DatabaseMigrations (Adatbázis-migrálások) | Nem | Nem |
> | services | Igen | Yes |
> | szolgáltatások/projektek | Igen | Yes |
> | SqlMigrationServices | Igen | Yes |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Igen | Yes |
> | ResourceGuards | Igen | Yes |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | fiókok/megosztások | Nem | Nem |
> | fiókok/ megosztások / adatkészletek | Nem | Nem |
> | fiókok / megosztások / meghívók | Nem | Nem |
> | fiókok / megosztások / szolgáltatómegosztások előiratai | Nem | Nem |
> | fiókok / megosztások / synchronizationSettings | Nem | Nem |
> | fiókok/megosztások előiratai | Nem | Nem |
> | accounts / sharesubscriptions / consumerSourceDataSets | Nem | Nem |
> | accounts / sharesubscriptions / datasetmappings | Nem | Nem |
> | fiókok / megosztások előiratai / eseményindítók | Nem | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Szerverek | Igen | Yes |
> | servers /advisors | Nem | Nem |
> | kiszolgálók /kulcsok | Nem | Nem |
> | servers / privateEndpointConnectionProxies | Nem | Nem |
> | servers /privateEndpointConnections | Nem | Nem |
> | servers /privateLinkResources | Nem | Nem |
> | servers /queryTexts | Nem | Nem |
> | servers / recoverableServers | Nem | Nem |
> | servers / resetQueryPerformanceInsightData | Nem | Nem |
> | servers /start | Nem | Nem |
> | servers / stop | Nem | Nem |
> | servers /topQueryStatistics | Nem | Nem |
> | servers /virtualNetworkRules | Nem | Nem |
> | servers /waitStatistics | Nem | Nem |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | flexibleServers (rugalmas kiszolgáló) | Igen | Yes |
> | Szerverek | Igen | Yes |
> | servers /advisors | Nem | Nem |
> | kiszolgálók/kulcsok | Nem | Nem |
> | servers / privateEndpointConnectionProxies | Nem | Nem |
> | servers /privateEndpointConnections | Nem | Nem |
> | servers /privateLinkResources | Nem | Nem |
> | servers /queryTexts | Nem | Nem |
> | servers / recoverableServers | Nem | Nem |
> | servers / resetQueryPerformanceInsightData | Nem | Nem |
> | servers / start | Nem | Nem |
> | kiszolgálók / leállítás | Nem | Nem |
> | servers /topQueryStatistics | Nem | Nem |
> | servers /upgrade | Nem | Nem |
> | servers /virtualNetworkRules | Nem | Nem |
> | servers /waitStatistics | Nem | Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Igen | Yes |
> | serverGroups | Igen | Yes |
> | serverGroupsv2 | Igen | Yes |
> | Szerverek | Igen | Yes |
> | servers /advisors | Nem | Nem |
> | kiszolgálók /kulcsok | Nem | Nem |
> | servers / privateEndpointConnectionProxies | Nem | Nem |
> | servers /privateEndpointConnections | Nem | Nem |
> | servers /privateLinkResources | Nem | Nem |
> | servers /queryTexts | Nem | Nem |
> | servers / recoverableServers | Nem | Nem |
> | servers / resetQueryPerformanceInsightData | Nem | Nem |
> | servers /topQueryStatistics | Nem | Nem |
> | servers /virtualNetworkRules | Nem | Nem |
> | servers /waitStatistics | Nem | Nem |
> | serversv2 | Igen | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | artifactSources | Igen | Yes |
> | bevezetések | Igen | Yes |
> | serviceTopologies (szolgáltatástopológiák) | Igen | Yes |
> | serviceTopologies / services | Igen | Yes |
> | serviceTopologies / services / serviceUnits | Igen | Yes |
> | lépések | Igen | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazáscsoportok | Igen | Yes |
> | alkalmazáscsoportok / alkalmazások | Nem | Nem |
> | alkalmazáscsoportok / asztali számítógépek | Nem | Nem |
> | applicationgroups / startmenuitems | Nem | Nem |
> | gazdagépkészletek | Igen | Yes |
> | hostpools / msixpackages | Nem | Nem |
> | hostpools /sessionhosts | Nem | Nem |
> | hostpools / sessionhosts / usersessions | Nem | Nem |
> | hostpools / usersessions | Nem | Nem |
> | scalingPlans | Igen | Yes |
> | munkaterületek | Igen | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Igen | Yes |
> | ElasticPools / IotHubTenants | Igen | Yes |
> | ElasticPools / IotHubTenants / securitySettings | Nem | Nem |
> | IotHubs | Igen | Yes |
> | IotHubs / eventGridFilters | Nem | Nem |
> | IotHubs / securitySettings | Nem | Nem |
> | ProvisioningServices (Kiépítési szolgáltatások) | Igen | Yes |
> | Használat | Nem | Nem |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | fiókok /példányok | Igen | Yes |
> | registeredSubscriptions | Nem | Nem |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Csővezetékek | Igen | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Vezérlők | Igen | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | labcenters | Igen | Yes |
> | Labs | Igen | Yes |
> | tesztkörnyezetek/környezetek | Igen | Yes |
> | labs / serviceRunners | Igen | Yes |
> | labs / virtualMachines | Igen | Yes |
> | Menetrend | Igen | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Igen | Yes |
> | digitalTwinsInstances /endpoints | Nem | Nem |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | cassandraClusters | Igen | Yes |
> | databaseAccountNames (adatbázis-fiókok nevei) | Nem | Nem |
> | databaseAccounts (adatbázis-fiókok) | Igen | Yes |
> | restorableDatabaseAccounts | Nem | Nem |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Tartományok | Igen | Yes |
> | domains / domainOwnershipIdentifiers | Nem | Nem |
> | generateSsoRequest | Nem | Nem |
> | topLevelTartományok | Nem | Nem |
> | validateDomainRegistrationInformation | Nem | Nem |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nem | Nem |
> | lcsprojects / clouddeployments | Nem | Nem |
> | lcsprojects /connectors | Nem | Nem |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Címek | Igen | Yes |
> | orderCollections | Igen | Yes |
> | rendelések | Igen | Yes |
> | productFamiliesMetadata | Nem | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | services | Igen | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Tartományok | Igen | Yes |
> | tartományok /témakörök | Nem | Nem |
> | eventSubscriptions (eventSubscriptions) | Nem | Nem |
> | extensionTopics (bővítménytopikák) | Nem | Nem |
> | partnerNamespaces | Igen | Yes |
> | partnerNamespaces / eventChannels | Nem | Nem |
> | partnerRegistrations | Igen | Yes |
> | partnertopics | Igen | Yes |
> | partnertopics / eventSubscriptions | Nem | Nem |
> | systemTopics | Igen | Yes |
> | systemTopics / eventSubscriptions | Nem | Nem |
> | Témák | Igen | Yes |
> | topicTypes (témakörtípusok) | Nem | Nem |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Klaszterek | Igen | Yes |
> | Névterek | Igen | Yes |
> | névterek /engedélyezési szabályok | Nem | Nem |
> | névterek / disasterrecoveryconfigs | Nem | Nem |
> | névterek / eventhubs | Nem | Nem |
> | névterek / eventhubs /authorizationrules | Nem | Nem |
> | névterek / eventhubs /consumergroups | Nem | Nem |
> | névterek /networkrulesets | Nem | Nem |
> | namespaces / privateEndpointConnections | Nem | Nem |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Igen | Yes |

## <a name="microsoftfalcon"></a>Microsoft.Fog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Névterek | Igen | Yes |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | featureConfigurations (funkciókonfigurációk) | Nem | Nem |
> | featureProviderNamespaces | Nem | Nem |
> | featureProviders | Nem | Nem |
> | funkciók | Nem | Nem |
> | Szolgáltatók | Nem | Nem |
> | subscriptionFeatureRegistrations | Nem | Nem |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Beiratkozik | Nem | Nem |
> | galleryitems | Nem | Nem |
> | generateartifactaccessuri | Nem | Nem |
> | myareas | Nem | Nem |
> | myareas / területek | Nem | Nem |
> | myareas / területek /területek | Nem | Nem |
> | myareas / területek / területek / katalógusok | Nem | Nem |
> | myareas / területek / katalógusok | Nem | Nem |
> | myareas / galleryitems | Nem | Nem |
> | Regisztráció | Nem | Nem |
> | resources | Nem | Nem |
> | retrieveresourcesbyid | Nem | Nem |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Igen | Yes |
> | autoManagedVmConfigurationProfiles | Igen | Yes |
> | configurationProfileAssignments | Nem | Nem |
> | guestConfigurationAssignments | Nem | Nem |
> | Szoftver | Nem | Nem |
> | softwareUpdateProfile | Nem | Nem |
> | softwareUpdates (szoftver frissítés) | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Igen | Yes |
> | sapMonitors (sapMonitors) | Igen | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Igen | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | clusterPools (fürtkészletek) | Igen | Yes |
> | fürtkészletek/fürtök | Igen | Yes |
> | Klaszterek | Igen | Yes |
> | fürtök /alkalmazások | Nem | Nem |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | healthBots | Igen | Yes |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | services | Igen | Yes |
> | szolgáltatások / iomtconnectors | Nem | Nem |
> | szolgáltatások / iomtconnectors / kapcsolatok | Nem | Nem |
> | szolgáltatások / iomtconnectors / leképezések | Nem | Nem |
> | services / privateEndpointConnectionProxies | Nem | Nem |
> | services / privateEndpointConnections | Nem | Nem |
> | szolgáltatások / privateLinkResources | Nem | Nem |
> | munkaterületek | Igen | Yes |
> | munkaterületek /dicomservices | Igen | Yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Gépek | Igen | Yes |
> | gépek / kierőltethető kizárások | Nem | Nem |
> | gépek/bővítmények | Igen | Yes |
> | machines /installPatches | Nem | Nem |
> | machines / privateLinkScopes | Nem | Nem |
> | privateLinkScopes | Igen | Yes |
> | privateLinkScopes / privateEndpointConnectionProxies | Nem | Nem |
> | privateLinkScopes / privateEndpointConnections | Nem | Nem |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | dataManagers | Igen | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | eszközök | Igen | Yes |
> | networkfunctions | Igen | Yes |
> | networkFunctionVendors | Nem | Nem |
> | registeredSubscriptions | Nem | Nem |
> | Szállítók | Nem | Nem |
> | Szállítók/szállítók | Nem | Nem |
> | Beszállítók/szállítók/ előzetes verziókelői | Nem | Nem |
> | virtualNetworkFunctions | Igen | Yes |
> | virtualNetworkFunctionVendors | Nem | Nem |

## <a name="microsofthydra"></a>Microsoft.Fog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Összetevők | Igen | Yes |
> | networkScopes | Igen | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Munkahelyek | Igen | Yes |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | actionGroups (műveletcsoportok) | Igen | Yes |
> | activityLogAlerts | Igen | Yes |
> | riasztási szabály | Igen | Yes |
> | automatikus méretezési beállítások | Igen | Yes |
> | Összetevők | Igen | Yes |
> | components / linkedStorageAccounts | Nem | Nem |
> | components / ProactiveDetectionConfigs | Nem | Nem |
> | diagnosticSettings | Nem | Nem |
> | guestDiagnosticSettings | Igen | Yes |
> | guestDiagnosticSettingsAssociation | Igen | Yes |
> | logprofiles | Igen | Yes |
> | metricAlerts (mérőszámok) | Igen | Yes |
> | privateLinkScopes | Igen | Yes |
> | privateLinkScopes / privateEndpointConnections | Nem | Nem |
> | privateLinkScopes / scopedResources | Nem | Nem |
> | queryPacks | Igen | Yes |
> | queryPacks / lekérdezések | Nem | Nem |
> | scheduledQueryRules | Igen | Yes |
> | webtesztek | Igen | Yes |
> | munkafüzetek | Igen | Yes |
> | workbooktemplates (munkafüzet-feltallók) | Igen | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings (diagnosztikai beállítások) | Nem | Nem |
> | diagnosticSettingsCategories | Nem | Nem |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | appTemplates (appTemplates) | Nem | Nem |
> | IoTApps | Igen | Yes |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | defenderSettings | Nem | Nem |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Graph | Igen | Yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | deletedManagedHSMs | Nem | Nem |
> | töröltvaults | Nem | Nem |
> | hsmPools | Igen | Yes |
> | managedHSMs | Igen | Yes |
> | Boltívek | Igen | Yes |
> | tárolók / accessPolicies | Nem | Nem |
> | tárolók / eventGridFilters | Nem | Nem |
> | tárolók /kulcsok | Nem | Nem |
> | tárolók / kulcsok / verziók | Nem | Nem |
> | tárolók /titkos kulcsok | Nem | Nem |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | connectedClusters (csatlakoztatott fürtök) | Igen | Yes |
> | registeredSubscriptions | Nem | Nem |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Kiterjesztés | Nem | Nem |
> | sourceControlConfigurations (forrásvezérlő konfigurációi) | Nem | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Klaszterek | Igen | Yes |
> | fürtök /attacheddatabaseconfigurations | Nem | Nem |
> | fürtök/adatbázisok | Nem | Nem |
> | fürtök / adatbázisok / adatkapcsolatok | Nem | Nem |
> | fürtök / adatbázisok / eventhubconnections | Nem | Nem |
> | fürtök / adatbázisok / principalassignments | Nem | Nem |
> | fürtök / adatbázisok / szkriptek | Nem | Nem |
> | fürtök /adatkapcsolatok | Nem | Nem |
> | fürtök / principalassignments | Nem | Nem |
> | fürtök/megosztott entitások | Nem | Nem |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | labaccounts | Igen | Nem |
> | labplans | Igen | Yes |
> | Labs | Igen | Yes |
> | felhasználók | Nem | Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Igen | Yes |
> | integrationAccounts (integrationAccounts) | Igen | Yes |
> | integrationServiceEnvironments | Igen | Yes |
> | integrationServiceEnvironments / managedApis | Nem | Nem |
> | isolatedEnvironments (elkülönített környezetek) | Igen | Yes |
> | Munkafolyamatok | Igen | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | commitmentPlans (kötelezettségvállalásisíkok) | Igen | Yes |
> | webServices | Igen | Yes |
> | Munkaterületek | Igen | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | modelinventories (modellinventories) | Igen | Yes |
> | virtualclusters (virtuális fürtök) | Igen | Yes |
> | munkaterületek | Igen | Yes |
> | munkaterületek / batchEndpoints | Igen | Yes |
> | munkaterületek / batchEndpoints / üzemelő példányok | Igen | Yes |
> | munkaterületek / batchEndpoints / üzemelő példányok / feladatok | Nem | Nem |
> | munkaterületek / batchEndpoints / feladatok | Nem | Nem |
> | munkaterületek/kódok | Nem | Nem |
> | munkaterületek / kódok / verziók | Nem | Nem |
> | munkaterületek /számítási erőforrások | Nem | Nem |
> | munkaterületek / adatok | Nem | Nem |
> | munkaterületek/adattárak | Nem | Nem |
> | munkaterületek /környezetek | Nem | Nem |
> | workspaces / eventGridFilters | Nem | Nem |
> | munkaterületek /feladatok | Nem | Nem |
> | munkaterületek / címkézésFeladatok | Nem | Nem |
> | munkaterületek /linkedServices | Nem | Nem |
> | munkaterületek /modellek | Nem | Nem |
> | munkaterületek / modellek / verziók | Nem | Nem |
> | munkaterületek / onlineEndpoints | Igen | Yes |
> | munkaterületek / onlineEndpoints / üzemelő példányok | Igen | Yes |

> [!NOTE]
> A munkaterület-címkék nem propagálnak a számítási fürtökre és számítási példányra.

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | applyUpdates (applyUpdates) | Nem | Nem |
> | configurationAssignments | Nem | Nem |
> | maintenanceConfigurations (karbantartás konfigurálása) | Igen | Yes |
> | publicMaintenanceConfigurations | Nem | Nem |
> | Frissítések | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Identitások | Nem | Nem |
> | userAssignedIdentities (userAssignedIdentities) | Igen | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Igen | Yes |
> | managedNetworks / managedNetworkGroups | Igen | Yes |
> | managedNetworks / managedNetworkPeeringPolicies | Igen | Yes |
> | értesítés | Igen | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nem | Nem |
> | registrationAssignments | Nem | Nem |
> | registrationDefinitions (registrationDefinitions) | Nem | Nem |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | getEntities (Entitások lekérte) | Nem | Nem |
> | managementGroups (felügyeleti csoportok) | Nem | Nem |
> | managementGroups / beállítások | Nem | Nem |
> | resources | Nem | Nem |
> | startTenantBackfill | Nem | Nem |
> | tenantBackfillStatus | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | fiókok /létrehozók | Igen | Yes |
> | accounts / eventGridFilters | Nem | Nem |
> | fiókok /privateAtlases | Igen | Yes |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | macc | Nem | Nem |
> | Kínál | Nem | Nem |
> | offerTypes (ajánlattípusok) | Nem | Nem |
> | offerTypes /publishers | Nem | Nem |
> | offerTypes / közzétevők / ajánlatok | Nem | Nem |
> | offerTypes / publishers / offers /plans | Nem | Nem |
> | offerTypes / publishers / offers / plans / agreements | Nem | Nem |
> | offerTypes / publishers / offers / plans / configs | Nem | Nem |
> | offerTypes / publishers / offers / plans / configs / importImage | Nem | Nem |
> | privategalleryitems | Nem | Nem |
> | privateStoreClient | Nem | Nem |
> | privateStores | Nem | Nem |
> | privateStores / AdminRequestApprovals | Nem | Nem |
> | privateStores / ajánlatok | Nem | Nem |
> | privateStores / offers / acknowledgeNotification | Nem | Nem |
> | privateStores / queryNotificationsState | Nem | Nem |
> | privateStores / RequestApprovals | Nem | Nem |
> | privateStores / requestApprovals / query | Nem | Nem |
> | privateStores / requestApprovals / egyedsík | Nem | Nem |
> | Termékek | Nem | Nem |
> | Kiadók | Nem | Nem |
> | közzétevők/ajánlatok | Nem | Nem |
> | közzétevők/ ajánlatok/módosítások | Nem | Nem |
> | Regisztráció | Nem | Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Igen | Yes |
> | updateCommunicationPreference | Nem | Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceRendelés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Megállapodások | Nem | Nem |
> | offertypes (ajánlattípusok) | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | mediaservices | Igen | Yes |
> | mediaservices /accountFilters | Nem | Nem |
> | mediaservices /assets | Nem | Nem |
> | mediaservices / assets / assetFilters | Nem | Nem |
> | mediaservices / contentKeyPolicies | Nem | Nem |
> | mediaservices / eventGridFilters | Nem | Nem |
> | mediaservices /graphInstances | Nem | Nem |
> | mediaservices / graphTopologies | Nem | Nem |
> | mediaservices / liveEventOperations | Nem | Nem |
> | mediaservices /liveEvents | Igen | Yes |
> | mediaservices / liveEvents / liveOutputs | Nem | Nem |
> | mediaservices /liveOutputOperations | Nem | Nem |
> | mediaservices / mediaGraphs | Nem | Nem |
> | mediaservices / privateEndpointConnectionOperations | Nem | Nem |
> | mediaservices / privateEndpointConnectionProxies | Nem | Nem |
> | mediaservices / privateEndpointConnections | Nem | Nem |
> | mediaservices / streamingEndpointOperations | Nem | Nem |
> | mediaservices / streamingEndpoints | Igen | Yes |
> | mediaservices /streamingLocators | Nem | Nem |
> | mediaservices / streamingPolicies | Nem | Nem |
> | mediaservices /transforms | Nem | Nem |
> | mediaservices / transforms / jobs | Nem | Nem |
> | videoAnalyzers | Igen | Yes |
> | videoAnalyzers / edgeModules | Nem | Nem |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | appClusters (appClusterek) | Igen | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Igen | Yes |
> | migrateprojects | Igen | Yes |
> | moveCollections | Igen | Yes |
> | Projektek | Igen | Yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Igen | Yes |
> | objectAnchorsAccounts | Igen | Yes |
> | objectUnderstandingAccounts | Igen | Yes |
> | remoteRenderingAccounts | Igen | Yes |
> | spatialAnchorsAccounts | Igen | Yes |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Hálózatok | Igen | Yes |
> | networks /sites | Igen | Yes |
> | packetCores (csomagmagok) | Igen | Yes |
> | Sims | Igen | Yes |
> | sims / simProfiles | Igen | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | netAppAccounts (netAppAccounts) | Igen | Nem |
> | netAppAccounts / accountBackups | Nem | Nem |
> | netAppAccounts / capacityPools | Igen | Nem |
> | netAppAccounts / capacityPools /volumes | Igen | Nem |
> | netAppAccounts / capacityPools / kötetek / pillanatképek | Nem | Nem |
> | netAppAccounts / volumeGroups | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Igen | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Igen | Yes |
> | applicationSecurityGroups | Igen | Yes |
> | azureFirewallFqdnTags | Nem | Nem |
> | azureFirewalls | Igen | Nem |
> | bastionHosts | Igen | Nem |
> | bgpServiceCommunities (bgpServiceCommunities) | Nem | Nem |
> | Kapcsolatok | Igen | Yes |
> | ddosCustomPolicies | Igen | Yes |
> | ddosProtectionPlans | Igen | Yes |
> | dnsOperationStatuses | Nem | Nem |
> | dnszones | Igen | Yes |
> | dnszones / A | Nem | Nem |
> | dnszones / AAAA | Nem | Nem |
> | dnszones / all | Nem | Nem |
> | dnszones / CAA | Nem | Nem |
> | dnszones / CNAME | Nem | Nem |
> | dnszones / MX | Nem | Nem |
> | dnszones / NS | Nem | Nem |
> | dnszones / PTR | Nem | Nem |
> | dnszones / recordsets | Nem | Nem |
> | dnszones / SOA | Nem | Nem |
> | dnszones / SRV | Nem | Nem |
> | dnszones / TXT | Nem | Nem |
> | expressRouteCircuits | Igen | Yes |
> | expressRouteCrossConnections | Igen | Yes |
> | expressRouteGateways | Igen | Yes |
> | expressRoutePorts | Igen | Yes |
> | expressRouteServiceProviders | Nem | Nem |
> | firewallPolicies | Igen | Yes |
> | frontdoors (előtők) | Igen, de korlátozott (lásd [az alábbi megjegyzést)](#frontdoor) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Igen, de korlátozott (lásd [az alábbi megjegyzést)](#frontdoor) | No |
> | frontdoorWebApplicationFirewallPolicies | Igen, de korlátozott (lásd [az alábbi megjegyzést)](#frontdoor) | Yes |
> | getDnsResourceReference | Nem | Nem |
> | internalNotify | Nem | Nem |
> | ipGroups (IP-csoportok) | Igen | Yes |
> | loadBalancers | Igen | Yes |
> | localNetworkGateways | Igen | Yes |
> | natGateways | Igen | Yes |
> | networkIntentPolicies | Igen | Yes |
> | networkInterfaces | Igen | Yes |
> | networkProfiles | Igen | Yes |
> | networkSecurityGroups | Igen | Yes |
> | networkWatchers | Igen | Yes |
> | networkWatchers / connectionMonitors | Igen | Nem |
> | networkWatchers / flowLogs | Igen | Nem |
> | networkWatchers /lenses | Igen | Nem |
> | networkWatchers / pingMeshes | Igen | Nem |
> | p2sVpnGateways | Igen | Yes |
> | privateDnsOperationStatuses | Nem | Nem |
> | privateDnsZones | Igen | Yes |
> | privateDnsZones / A | Nem | Nem |
> | privateDnsZones / AAAA | Nem | Nem |
> | privateDnsZones / all | Nem | Nem |
> | privateDnsZones / CNAME | Nem | Nem |
> | privateDnsZones / MX | Nem | Nem |
> | privateDnsZones / PTR | Nem | Nem |
> | privateDnsZones / SOA | Nem | Nem |
> | privateDnsZones / SRV | Nem | Nem |
> | privateDnsZones / TXT | Nem | Nem |
> | privateDnsZones / virtualNetworkLinks | Igen | Yes |
> | privateEndpoints | Igen | Yes |
> | privateLinkServices | Igen | Yes |
> | publicIPAddresses | Igen | Yes |
> | publicIPPrefixes | Igen | Yes |
> | routeFilters | Igen | Yes |
> | routeTables | Igen | Yes |
> | serviceEndpointPolicies | Igen | Yes |
> | trafficManagerGeographicHierarchies | Nem | Nem |
> | trafficmanagerprofiles | Igen | Yes |
> | trafficmanagerprofiles/heatMaps | Nem | Nem |
> | trafficManagerUserMetricsKeys | Nem | Nem |
> | virtualHubs | Igen | Yes |
> | virtualNetworkGateways | Igen | Yes |
> | virtualNetworks | Igen | Yes |
> | virtualNetworks /alhálózatok | Nem | Nem |
> | virtualNetworkTaps | Igen | Yes |
> | virtualWans | Igen | Nem |
> | vpnGateways | Igen | Yes |
> | vpnSites (vpnSites) | Igen | Yes |
> | webApplicationFirewallPolicies | Igen | Yes |

<a id="frontdoor"></a>

> [!NOTE]
> A Azure Front Door Service alkalmazhat címkéket az erőforrás létrehozásakor, de a címkék frissítése vagy hozzáadása jelenleg nem támogatott.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Nem | Nem |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Névterek | Igen | Nem |
> | névterek / notificationHubs | Igen | Nem |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | osNamespaces (osNamespaces) | Igen | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | HyperVSites (HiperVSites) | Igen | Yes |
> | ImportSites (Importálási beállítások) | Igen | Yes |
> | Főkiszolgálók | Igen | Yes |
> | Kiszolgálóiwebhelyek | Igen | Yes |
> | VMwareSites | Igen | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Klaszterek | Igen | Yes |
> | deletedWorkspaces | Nem | Nem |
> | linkTargets | Nem | Nem |
> | querypacks | Igen | Yes |
> | storageInsightConfigs | Nem | Nem |
> | munkaterületek | Igen | Yes |
> | munkaterületek / adatokExportálások | Nem | Nem |
> | munkaterületek / adatforrások | Nem | Nem |
> | munkaterületek / linkedServices | Nem | Nem |
> | workspaces / linkedStorageAccounts | Nem | Nem |
> | munkaterületek / metaadatok | Nem | Nem |
> | munkaterületek /lekérdezés | Nem | Nem |
> | workspaces / scopedPrivateLinkProxies | Nem | Nem |
> | munkaterületek / storageInsightConfigs | Nem | Nem |
> | munkaterületek /táblák | Nem | Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | felügyeleti társítások | Nem | Nem |
> | managementconfigurations (felügyeleti konfigurációk) | Igen | Yes |
> | megoldások | Igen | Yes |
> | Kilátás nyílik | Igen | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | cdnPeeringPrefixes | Nem | Nem |
> | legacyPeerings | Nem | Nem |
> | peerAsns | Nem | Nem |
> | társviszonyok | Igen | Yes |
> | peeringServiceCountries | Nem | Nem |
> | peeringServiceProviders | Nem | Nem |
> | társviszony-létesítésSzolgáltatások | Igen | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | attestations (attestations) | Nem | Nem |
> | eventGridFilters | Nem | Nem |
> | policyEvents | Nem | Nem |
> | policyMetadata | Nem | Nem |
> | policyStates | Nem | Nem |
> | policyTrackedResources | Nem | Nem |
> | szervizelések | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> |  -konzolok | Nem | Nem |
> | irányítópultok | Igen | Yes |
> | tenantconfigurations (bérlőkonfigurációk) | Nem | Nem |
> | UserSettings | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Igen | Yes |
> | Bérlők | Igen | Yes |
> | bérlők/munkaterületek | Nem | Nem |
> | workspaceCollections (munkaterület-collections) | Igen | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | autoScaleVCores (automatikus skálázásVCores) | Igen | Yes |
> | Kapacitások | Igen | Yes |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | enterprisePolicies | Igen | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | deletedAccounts (Fiókok törlése) | Nem | Nem |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nem | Nem |
> | providerRegistrations / customRollouts | Nem | Nem |
> | providerRegistrations / defaultRollouts | Nem | Nem |
> | providerRegistrations / resourceTypeRegistrations | Nem | Nem |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | deletedAccounts (Fiókok törlése) | Nem | Nem |
> | getDefaultAccount | Nem | Nem |
> | removeDefaultAccount | Nem | Nem |
> | setDefaultAccount (SetDefaultAccount) | Nem | Nem |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Munkaterületek | Igen | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nem | Nem |
> | Boltívek | Igen | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Igen | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Névterek | Igen | Yes |
> | névterek /engedélyezési szabályok | Nem | Nem |
> | névterek /hibrid kapcsolat | Nem | Nem |
> | névterek / hibrid kapcsolat / engedélyezési szabályok | Nem | Nem |
> | namespaces / privateEndpointConnections | Nem | Nem |
> | névterek / wcfrelays | Nem | Nem |
> | névterek / wcfrelays / authorizationrules | Nem | Nem |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Készülékek | Igen | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Lekérdezések | Igen | Yes |
> | resourceChangeDetails | Nem | Nem |
> | resourceChanges (erőforrás-váltások) | Nem | Nem |
> | resources | Nem | Nem |
> | resourcesHistory | Nem | Nem |
> | subscriptionsStatus | Nem | Nem |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses (rendelkezésre állásiállapotok) | Nem | Nem |
> | childAvailabilityStatuses | Nem | Nem |
> | childResources | Nem | Nem |
> | emergingissues | Nem | Nem |
> | események | Nem | Nem |
> | impactedResources | Nem | Nem |
> | metaadatok | Nem | Nem |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Telepítések | Igen | Nem |
> | üzemelő példányok /műveletek | Nem | Nem |
> | deploymentScripts | Igen | Yes |
> | deploymentScripts / naplók | Nem | Nem |
> | Linkek | Nem | Nem |
> | Szolgáltatók | Nem | Nem |
> | resourceGroups (erőforráscsoportok) | Igen | Nem |
> | Előfizetések | Igen | Nem |
> | templateSpecs (sablon-specifikus) | Igen | Yes |
> | templateSpecs /versions | Igen | Yes |
> | Bérlők | Nem | Nem |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen | Yes |
> | resources | Igen | Yes |
> | saasresources | Nem | Nem |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Felhők | Igen | Yes |
> | VirtualMachines | Igen | Yes |
> | VirtualMachineTemplates | Igen | Yes |
> | VirtualNetworks | Igen | Yes |
> | vmmservers | Igen | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nem | Nem |
> | searchServices | Igen | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nem | Nem |
> | advancedThreatProtectionSettings | Nem | Nem |
> | riasztások | Nem | Nem |
> | alertsSuppressionRules | Nem | Nem |
> | allowedConnections (engedélyezett kapcsolat) | Nem | Nem |
> | applicationWhitelistings | Nem | Nem |
> | assessmentMetadata | Nem | Nem |
> | értékelések | Nem | Nem |
> | autoDismissAlertsRules | Nem | Nem |
> | automatizálások | Igen | Yes |
> | AutoProvisioningSettings | Nem | Nem |
> | Megfelelőség | Nem | Nem |
> | összekötők | Nem | Nem |
> | dataCollectionAgents | Nem | Nem |
> | eszközök | Nem | Nem |
> | deviceSecurityGroups | Nem | Nem |
> | discoveredSecuritySolutions | Nem | Nem |
> | externalSecuritySolutions | Nem | Nem |
> | InformationProtectionPolicies | Nem | Nem |
> | ingestionSettings | Nem | Nem |
> | Betekintést | Nem | Nem |
> | iotAlerts (iotAlerts) | Nem | Nem |
> | iotAlertTypes | Nem | Nem |
> | iotDefenderSettings | Nem | Nem |
> | iotRecommendations (iotRecommendations) | Nem | Nem |
> | iotRecommendationTypes | Nem | Nem |
> | iotSecuritySolutions | Igen | Yes |
> | iotSecuritySolutions / analyticsModels | Nem | Nem |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nem | Nem |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nem | Nem |
> | iotSecuritySolutions / iotAlerts | Nem | Nem |
> | iotSecuritySolutions / iotAlertTypes | Nem | Nem |
> | iotSecuritySolutions / iotRecommendations | Nem | Nem |
> | iotSecuritySolutions / iotRecommendationTypes | Nem | Nem |
> | iotSensors (iotSensors) | Nem | Nem |
> | iotSites (iotSites) | Nem | Nem |
> | jitNetworkAccessPolicies | Nem | Nem |
> | jitPolicies | Nem | Nem |
> | onPremiseIotSensors | Nem | Nem |
> | policies | Nem | Nem |
> | díjszabások | Nem | Nem |
> | regulatoryComplianceStandards | Nem | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nem | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nem | Nem |
> | secureScoreControlDefinitions | Nem | Nem |
> | secureScoreControls | Nem | Nem |
> | secureScores (secureScores) | Nem | Nem |
> | secureScores / secureScoreControls | Nem | Nem |
> | securityContacts | Nem | Nem |
> | biztonságMegoldások | Nem | Nem |
> | securitySolutionsReferenceData | Nem | Nem |
> | securityStatuses | Nem | Nem |
> | securityStatusesSummaries | Nem | Nem |
> | serverVulnerabilityAssessments | Nem | Nem |
> | beállítások | Nem | Nem |
> | sqlVulnerabilityAssessments | Nem | Nem |
> | alértékelések | Nem | Nem |
> | feladatok | Nem | Nem |
> | Topológiák | Nem | Nem |
> | workspaceSettings (munkaterület-beállítások) | Nem | Nem |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings (diagnosztikai beállítások) | Nem | Nem |
> | diagnosticSettingsCategories | Nem | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Összesítései | Nem | Nem |
> | alertRules (riasztásiszabály) | Nem | Nem |
> | alertRuleTemplates | Nem | Nem |
> | automationRules | Nem | Nem |
> | könyvjelzők | Nem | Nem |
> | Esetekben | Nem | Nem |
> | dataConnectors (adatcsatlakozók) | Nem | Nem |
> | dataConnectorsCheckRequirements | Nem | Nem |
> | Gazdagodás | Nem | Nem |
> | Entitások | Nem | Nem |
> | entityQueries (entitáslekérdezés) | Nem | Nem |
> | entityQueryTemplates (entityQueryTemplates) | Nem | Nem |
> | incidensek | Nem | Nem |
> | officeConsents | Nem | Nem |
> | beállítások | Nem | Nem |
> | threatIntelligence (fenyegetésintelligencia) | Nem | Nem |
> | megnézőlisták | Nem | Nem |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | konzolSzolgáltatások | Nem | Nem |
> | serialPorts | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Névterek | Igen | Yes |
> | névterek /engedélyezési szabályok | Nem | Nem |
> | névterek / disasterrecoveryconfigs | Nem | Nem |
> | névterek / eventgridfilters | Nem | Nem |
> | névterek /networkrulesets | Nem | Nem |
> | névterek / privateEndpointConnections | Nem | Nem |
> | névterek /üzenetsorok | Nem | Nem |
> | névterek / üzenetsorok / engedélyezési szabályok | Nem | Nem |
> | névterek /témakörök | Nem | Nem |
> | névterek / témakörök / engedélyezési szabályok | Nem | Nem |
> | névterek / témakörök / előfizetések | Nem | Nem |
> | névterek / témakörök / előfizetések / szabályok | Nem | Nem |
> | premiumMessagingRegions | Nem | Nem |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen | Yes |
> | Klaszterek | Igen | Yes |
> | fürtök /alkalmazások | Nem | Nem |
> | containerGroups | Igen | Yes |
> | containerGroupSets | Igen | Yes |
> | edgeclusters | Igen | Yes |
> | edgeclusters / applications | Nem | Nem |
> | managedclusters (felügyelt fürtök) | Igen | Yes |
> | managedclusters / alkalmazások | Nem | Nem |
> | managedclusters / alkalmazások /szolgáltatások | Nem | Nem |
> | managedclusters / applicationTypes | Nem | Nem |
> | managedclusters / applicationTypes / versions | Nem | Nem |
> | managedclusters / nodetypes | Nem | Nem |
> | Hálózatok | Igen | Yes |
> | titkos kulcstárolók | Igen | Yes |
> | titkos kulcstárak /tanúsítványok | Nem | Nem |
> | titkos kulcsok /titkos kulcsok | Nem | Nem |
> | volumes | Igen | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen | Yes |
> | containerGroups | Igen | Yes |
> | Átjárók | Igen | Yes |
> | Hálózatok | Igen | Yes |
> | Titkok | Igen | Yes |
> | volumes | Igen | Yes |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Linkerek | Nem | Nem |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nem | Nem |
> | providerRegistrations / resourceTypeRegistrations | Nem | Nem |
> | bevezetések | Igen | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | SignalR | Igen | Yes |
> | SignalR/ eventGridFilters | Nem | Nem |
> | WebPubSub | Igen | Yes |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | accounts /accountQuotaPolicies | Nem | Nem |
> | accounts / groupPolicies | Nem | Nem |
> | fiókok /feladatok | Nem | Nem |
> | fiókok / storageContainers | Nem | Nem |
> | images | Nem | Nem |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions (alkalmazásdefiníciók) | Igen | Yes |
> | alkalmazások | Igen | Yes |
> | jitRequests | Igen | Yes |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | longtermRetentionManagedInstance / longtermRetentionDatabase / longtermRetentionBackup | Nem | Nem |
> | longtermRetentionServer / longtermRetentionDatabase / longtermRetentionBackup | Nem | Nem |
> | managedInstances | Igen | Yes |
> | managedInstances /databases | Nem | Nem |
> | managedInstances / databases / backupShortTermRetentionPolicies | Nem | Nem |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Nem | Nem |
> | managedInstances / databases / vulnerabilityAssessments | Nem | Nem |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Nem | Nem |
> | managedInstances / encryptionProtector | Nem | Nem |
> | managedInstances / keys | Nem | Nem |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nem | Nem |
> | managedInstances / vulnerabilityAssessments | Nem | Nem |
> | Szerverek | Igen | Yes |
> | kiszolgálók /rendszergazdák | Nem | Nem |
> | servers /communicationLinks | Nem | Nem |
> | kiszolgálók /adatbázisok | Igen (lásd [az alábbi megjegyzést)](#sqlnote) | Yes |
> | servers /encryptionProtector | Nem | Nem |
> | servers /firewallRules | Nem | Nem |
> | kiszolgálók/kulcsok | Nem | Nem |
> | servers / restorableDroppedDatabases | Nem | Nem |
> | servers /serviceobjectives | Nem | Nem |
> | servers / tdeCertificates | Nem | Nem |
> | virtualClusters (virtuális fürtök) | Igen | Yes |

<a id="sqlnote"></a>

> [!NOTE]
> A Master adatbázis nem támogatja a címkéket, de más adatbázisok, például a Azure Synapse Analytics, támogatják a címkéket. Azure Synapse Analytics adatbázisoknak Aktív (nem szüneteltetett) állapotban kell lennie.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Igen | Yes |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nem | Nem |
> | SqlVirtualMachines | Igen | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | deletedAccounts (Fiókok törlése) | Nem | Nem |
> | storageAccounts (tárfiókok) | Igen | Yes |
> | storageAccounts / blobServices | Nem | Nem |
> | storageAccounts / fileServices | Nem | Nem |
> | storageAccounts / queueServices | Nem | Nem |
> | storageAccounts / services | Nem | Nem |
> | storageAccounts / services / metricDefinitions | Nem | Nem |
> | storageAccounts / tableServices | Nem | Nem |
> | Használat | Nem | Nem |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | amlFilesystems | Igen | Yes |
> | Elrejt | Igen | Yes |
> | gyorsítótárak / storageTargets | Nem | Nem |
> | usageModels (használatmodellek) | Nem | Nem |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nem | Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Yes |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices / munkafolyamatok | Nem | Nem |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Yes |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices / munkafolyamatok | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Yes |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices / munkafolyamatok | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Vezetők | Igen | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Klaszterek | Igen | Yes |
> | fürtök /privateEndpoints | Nem | Nem |
> | streamingjobs | Igen (lásd az alábbi megjegyzést) | Yes |

> [!NOTE]
> Nem adhat hozzá címkét, ha a streamingjobs fut. Címke hozzáadásához állítsa le az erőforrást.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Nem | Nem |
> | acceptOwnership (Tulajdonos elfogadása) | Nem | Nem |
> | acceptOwnershipStatus | Nem | Nem |
> | Aliasok | Nem | Nem |
> | mégse | Nem | Nem |
> | changeTenantRequest | Nem | Nem |
> | changeTenantStatus | Nem | Nem |
> | CreateSubscription (Előíró létrehozása) | Nem | Nem |
> | Engedélyezi | Nem | Nem |
> | policies | Nem | Nem |
> | átnevezés | Nem | Nem |
> | SubscriptionDefinitions (SubscriptionDefinitions) | Nem | Nem |
> | SubscriptionOperations (Előfizetési együttműködések) | Nem | Nem |
> | Előfizetések | Nem | Nem |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Igen | Yes |
> | munkaterületek | Igen | Yes |
> | munkaterületek / bigDataPools | Igen | Yes |
> | munkaterületek / operationStatuses | Nem | Nem |
> | workspaces / sqlDatabases | Igen | Yes |
> | workspaces / sqlPools | Igen | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Környezetben | Igen | Nem |
> | környezetek / accessPolicies | Nem | Nem |
> | környezetek/eseményforrások | Igen | Nem |
> | környezetek / privateEndpointConnectionProxies | Nem | Nem |
> | környezetek / privateEndpointConnections | Nem | Nem |
> | környezetek / privateLinkResources | Nem | Nem |
> | környezetek / referenceDataSets | Igen | Nem |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Üzletek | Igen | Yes |
> | stores / accessPolicies | Nem | Nem |
> | áruházak /szolgáltatások | Nem | Nem |
> | stores / services / tokens | Nem | Nem |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Igen | Yes |
> | imageTemplates / runOutputs | Nem | Nem |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | ArcZones | Igen | Yes |
> | ResourcePools (Erőforráskészletek) | Igen | Yes |
> | VCenterek | Igen | Yes |
> | VCenters / InventoryItems | Nem | Nem |
> | virtualmachines | Igen | Yes |
> | VirtualMachineTemplates | Igen | Yes |
> | VirtualNetworks | Igen | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes (dedikált felhőcsomópont) | Igen | Yes |
> | dedicatedCloudServices | Igen | Yes |
> | virtualMachines | Igen | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | eszközök | Igen | Yes |
> | registeredSubscriptions | Nem | Nem |
> | Szállítók | Nem | Nem |
> | szállítók/termékkódok | Nem | Nem |
> | szállítók /vnfs | Nem | Nem |
> | virtualNetworkFunctionSkus | Nem | Nem |
> | vnfs | Igen | Yes |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Nem |
> | Tervek | Igen | Nem |
> | registeredSubscriptions | Nem | Nem |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nem | Nem |
> | apiManagementAccounts / apiAcls | Nem | Nem |
> | apiManagementAccounts / apis | Nem | Nem |
> | apiManagementAccounts / apis / apiAcls | Nem | Nem |
> | apiManagementAccounts / apis / connectionAcls | Nem | Nem |
> | apiManagementAccounts / apis /connections | Nem | Nem |
> | apiManagementAccounts / apis / connections / connectionAcls | Nem | Nem |
> | apiManagementAccounts / apis / localizedDefinitions | Nem | Nem |
> | apiManagementAccounts / connectionAcls | Nem | Nem |
> | apiManagementAccounts /connections | Nem | Nem |
> | billingMeters (számlázási mérők) | Nem | Nem |
> | tanúsítványok | Igen | Yes |
> | connectionGateways | Igen | Yes |
> | Kapcsolatok | Igen | Yes |
> | customApis (egyéniapis) | Igen | Yes |
> | deletedSites (Törölt webhelyek) | Nem | Nem |
> | functionAppStacks | Nem | Nem |
> | generateGithubAccessTokenForAppserviceCLI | Nem | Nem |
> | hostingEnvironments | Igen | Yes |
> | hostingEnvironments / eventGridFilters | Nem | Nem |
> | hostingEnvironments / multiRolePools | Nem | Nem |
> | hostingEnvironments / workerPools | Nem | Nem |
> | kubeEnvironments | Igen | Yes |
> | közzétételHasználók | Nem | Nem |
> | Ajánlások | Nem | Nem |
> | resourceHealthMetadata | Nem | Nem |
> | runtimes (futtatás) | Nem | Nem |
> | serverFarms | Igen | Yes |
> | serverFarms / eventGridFilters | Nem | Nem |
> | serverFarms / firstPartyApps | Nem | Nem |
> | serverFarms / firstPartyApps / keyVaultSettings | Nem | Nem |
> | Helyek | Igen | Yes |
> | helyek /konfiguráció  | Nem | Nem |
> | sites / eventGridFilters | Nem | Nem |
> | sites / hostNameBindings | Nem | Nem |
> | sites /networkConfig | Nem | Nem |
> | sites /premieraddons | Igen | Yes |
> | helyek/tárolóhelyek | Igen | Yes |
> | sites / slots / eventGridFilters | Nem | Nem |
> | sites / slots / hostNameBindings | Nem | Nem |
> | helyek/ tárolóhelyek / networkConfig | Nem | Nem |
> | sourceControls (forrásvezérlők) | Nem | Nem |
> | staticSites (statikus webhelyek) | Igen | Yes |
> | Érvényesít | Nem | Nem |
> | verifyHostingEnvironmentVnet | Nem | Nem |
> | webAppStacks | Nem | Nem |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings (diagnosztikai beállítások) | Nem | Nem |
> | diagnosticSettingsCategories | Nem | Nem |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Igen | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | DeviceServices (Eszközszolgáltatások) | Igen | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | migrationAgents | Igen | Yes |
> | Munkaterhek | Igen | Yes |
> | számítási feladatok /példányok | Nem | Nem |
> | számítási feladatok /verziók | Nem | Nem |
> | számítási feladatok / verziók / összetevők | Nem | Nem |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címkézés a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Monitorok | Nem | Nem |

## <a name="next-steps"></a>Következő lépések

A címkék erőforrásokra való alkalmazásának elsajátításért lásd: Címkék használata [az Azure-erőforrások rendszerezése érdekében.](tag-resources.md)
