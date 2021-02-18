---
title: Erőforrások támogatásának címkézése
description: Megjeleníti, hogy mely Azure-erőforrástípusok támogatják a címkéket. Az összes Azure-szolgáltatás részleteit tartalmazza.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 73f58dbda0e7c073cf4a8d2e846132a102192e93
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635914"
---
# <a name="tag-support-for-azure-resources"></a>Azure-erőforrások támogatásának címkézése
Ez a cikk azt mutatja be, hogy az erőforrástípus támogatja-e a [címkéket](tag-resources.md). A címkével ellátott oszlopban szereplő **címke jelzi,** hogy az erőforrástípus rendelkezik-e tulajdonsággal a címkéhez. A **címke a Cost jelentésben** feliratú oszlop jelzi, hogy az erőforrástípus átadja-e a címkét a Cost jelentésnek. A költségeket címkék alapján tekintheti meg a [Cost Management Cost Analysis](../../cost-management-billing/costs/group-filter.md) és az [Azure számlázási számlájában és a napi használati adatokban](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket tartalmazó fájlokat, töltse le [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Ugrás erőforrás-szolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft. HRE](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. igazolás](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. automanage](#microsoftautomanage)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. Azure. Genf](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft. BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. számlázás](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. Blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Codespaces](#microsoftcodespaces)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft. felhasználás](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. Tárolószolgáltatás](#microsoftcontainerservice)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. Data Lake Store](#microsoftdatalakestore)
> - [Microsoft. DataMigration](#microsoftdatamigration)
> - [Microsoft. DataProtection](#microsoftdataprotection)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. segédösszetevője](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. kísérletezés](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. bepillantások](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kulcstartó](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrálás](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. notebookok](#microsoftnotebooks)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft. PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. Quantum](#microsoftquantum)
> - [Microsoft. Recoveryservices szolgáltatónál](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. ScVmm](#microsoftscvmm)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. szingularitás](#microsoftsingularity)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. előfizetés](#microsoftsubscription)
> - [Microsoft. szinapszis](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. HRE

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | DomainServices | Igen | Yes |
> | DomainServices / oucontainer | Nem | Nem |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nem | Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nem | Nem |
> | addsservices | Nem | Nem |
> | ügynökök | Nem | Nem |
> | anonymousapiusers | Nem | Nem |
> | konfiguráció | Nem | Nem |
> | naplók | Nem | Nem |
> | jelentések | Nem | Nem |
> | servicehealthmetrics | Nem | Nem |
> | services | Nem | Nem |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | advisorScore | Nem | Nem |
> | konfigurációk | Nem | Nem |
> | generateRecommendations | Nem | Nem |
> | metaadatok | Nem | Nem |
> | javaslatok | Nem | Nem |
> | fóliakondenzát | Nem | Nem |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | farmBeats | Igen | Yes |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | actionRules | Igen | Yes |
> | riasztások | Nem | Nem |
> | alertsList | Nem | Nem |
> | alertsMetaData | Nem | Nem |
> | alertsSummary | Nem | Nem |
> | alertsSummaryList | Nem | Nem |
> | smartDetectorAlertRules | Igen | Yes |
> | smartGroups | Nem | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kiszolgálók | Igen | Yes |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nem | Nem |
> | szolgáltatás | Igen | Yes |
> | validateServiceName | Nem | Nem |

> [!NOTE]
> Az Azure API Management csak az egyes szolgáltatásokhoz legfeljebb 15 címke név/érték párok létrehozását támogatja.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | configurationStores | Igen | Nem |
> | configurationStores / eventGridFilters | Nem | Nem |
> | configurationStores/-értékek | Nem | Nem |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Spring | Igen | Yes |
> | Spring/apps | Nem | Nem |
> | Spring/apps/üzemelő példányok | Nem | Nem |

## <a name="microsoftattestation"></a>Microsoft. igazolás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Igen | Yes |
> | defaultProviders | Nem | Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | Nem | Nem |
> | accessReviewScheduleSettings | Nem | Nem |
> | classicAdministrators | Nem | Nem |
> | dataAliases | Nem | Nem |
> | denyAssignments | Nem | Nem |
> | elevateAccess | Nem | Nem |
> | findOrphanRoleAssignments | Nem | Nem |
> | zárak | Nem | Nem |
> | engedélyek | Nem | Nem |
> | policyAssignments | Nem | Nem |
> | policyDefinitions | Nem | Nem |
> | policyExemptions | Nem | Nem |
> | policySetDefinitions | Nem | Nem |
> | privateLinkAssociations | Nem | Nem |
> | providerOperations | Nem | Nem |
> | resourceManagementPrivateLinks | Igen | Yes |
> | roleAssignments | Nem | Nem |
> | roleAssignmentsUsageMetrics | Nem | Nem |
> | roleDefinitions | Nem | Nem |

## <a name="microsoftautomanage"></a>Microsoft. automanage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | configurationProfileAssignments | Nem | Nem |
> | configurationProfilePreferences | Igen | Yes |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Igen | Yes |
> | automationAccounts/konfigurációk | Igen | Yes |
> | automationAccounts/feladatok | Nem | Nem |
> | automationAccounts / privateEndpointConnectionProxies | Nem | Nem |
> | automationAccounts / privateEndpointConnections | Nem | Nem |
> | automationAccounts / privateLinkResources | Nem | Nem |
> | automationAccounts/runbookok | Igen | Yes |
> | automationAccounts / softwareUpdateConfigurations | Nem | Nem |
> | automationAccounts/webhookok | Nem | Nem |

> [!NOTE]
> Azure Automation csak az egyes Automation-erőforrásokhoz legfeljebb 15 címke név/érték párok létrehozását támogatja.

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | privateClouds | Igen | Yes |
> | privateClouds/bővítmények | Nem | Nem |
> | privateClouds/engedélyek | Nem | Nem |
> | privateClouds/fürtök | Nem | Nem |
> | privateClouds / globalReachConnections | Nem | Nem |
> | privateClouds / hcxEnterpriseSites | Nem | Nem |
> | privateClouds / workloadNetworks | Nem | Nem |
> | privateClouds / workloadNetworks / dhcpConfigurations | Nem | Nem |
> | privateClouds/workloadNetworks/átjárók | Nem | Nem |
> | privateClouds / workloadNetworks / portMirroringProfiles | Nem | Nem |
> | privateClouds/workloadNetworks/szegmensek | Nem | Nem |
> | privateClouds / workloadNetworks / virtualMachines | Nem | Nem |
> | privateClouds / workloadNetworks / vmGroups | Nem | Nem |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genf

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | környezetben | Nem | Nem |
> | környezetek/fiókok | Nem | Nem |
> | környezetek/fiókok/névterek | Nem | Nem |
> | környezetek/fiókok/névterek/konfigurációk | Nem | Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Igen | Nem |
> | b2ctenants | Nem | Nem |
> | guestUsages | Igen | Yes |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | dataControllers | Igen | Yes |
> | postgresInstances | Igen | Yes |
> | sqlManagedInstances | Igen | Yes |
> | sqlServerInstances | Igen | Yes |
> | sqlServerRegistrations | Igen | Yes |
> | sqlServerRegistrations / sqlServers | Nem | Nem |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Nem | Nem |
> | edgeSubscriptions | Igen | Yes |
> | linkedSubscriptions | Igen | Yes |
> | regisztrációk | Igen | Yes |
> | regisztrációk/customerSubscriptions | Nem | Nem |
> | regisztrációk/termékek | Nem | Nem |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fürtök | Igen | Yes |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Igen | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Igen | Yes |
> | batchAccounts/tanúsítványok | Nem | Nem |
> | batchAccounts/készletek | Nem | Nem |

## <a name="microsoftbilling"></a>Microsoft. számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nem | Nem |
> | billingAccounts/szerződések | Nem | Nem |
> | billingAccounts / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles | Nem | Nem |
> | billingAccounts / billingProfiles / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingProfiles / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts/billingProfiles/ügyfelek | Nem | Nem |
> | billingAccounts/billingProfiles/utasítások | Nem | Nem |
> | billingAccounts/billingProfiles/számlák | Nem | Nem |
> | billingAccounts/billingProfiles/számlák/árlista | Nem | Nem |
> | billingAccounts/billingProfiles/számlák/tranzakciók | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nem | Nem |
> | billingAccounts/billingProfiles/invoiceSections/termékek | Nem | Nem |
> | billingAccounts/billingProfiles/invoiceSections/termékek/átvitel | Nem | Nem |
> | billingAccounts/billingProfiles/invoiceSections/termékek/updateAutoRenew | Nem | Nem |
> | billingAccounts/billingProfiles/invoiceSections/tranzakciók | Nem | Nem |
> | billingAccounts/billingProfiles/invoiceSections/Transfers | Nem | Nem |
> | billingAccounts / BillingProfiles / patchOperations | Nem | Nem |
> | billingAccounts / billingProfiles / paymentMethods | Nem | Nem |
> | billingAccounts/billingProfiles/házirendek | Nem | Nem |
> | billingAccounts/billingProfiles/árlista | Nem | Nem |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nem | Nem |
> | billingAccounts/billingProfiles/termékek | Nem | Nem |
> | billingAccounts/billingProfiles/foglalások | Nem | Nem |
> | billingAccounts/billingProfiles/tranzakciók | Nem | Nem |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Nem | Nem |
> | billingAccounts / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingSubscriptions | Nem | Nem |
> | billingAccounts/billingSubscriptions/számlák | Nem | Nem |
> | billingAccounts / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / createInvoiceSectionOperations | Nem | Nem |
> | billingAccounts/ügyfelek | Nem | Nem |
> | billingAccounts/ügyfelek/billingPermissions | Nem | Nem |
> | billingAccounts/ügyfelek/billingSubscriptions | Nem | Nem |
> | billingAccounts/ügyfelek/initiateTransfer | Nem | Nem |
> | billingAccounts/ügyfelek/szabályzatok | Nem | Nem |
> | billingAccounts/ügyfelek/termékek | Nem | Nem |
> | billingAccounts/ügyfelek/tranzakciók | Nem | Nem |
> | billingAccounts/ügyfelek/átvitelek | Nem | Nem |
> | billingAccounts/részlegek | Nem | Nem |
> | billingAccounts/részlegek/billingPermissions | Nem | Nem |
> | billingAccounts/részlegek/billingRoleAssignments | Nem | Nem |
> | billingAccounts/részlegek/billingRoleDefinitions | Nem | Nem |
> | billingAccounts / enrollmentAccounts | Nem | Nem |
> | billingAccounts / enrollmentAccounts / billingPermissions | Nem | Nem |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Nem | Nem |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Nem | Nem |
> | billingAccounts/számlák | Nem | Nem |
> | billingAccounts/számlák/tranzakciók | Nem | Nem |
> | billingAccounts / invoiceSections | Nem | Nem |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nem | Nem |
> | billingAccounts / invoiceSections / billingSubscriptions | Nem | Nem |
> | billingAccounts/invoiceSections/billingSubscriptions/átvitel | Nem | Nem |
> | billingAccounts/invoiceSections/Jogosultságszint-emelés | Nem | Nem |
> | billingAccounts / invoiceSections / initiateTransfer | Nem | Nem |
> | billingAccounts / invoiceSections / patchOperations | Nem | Nem |
> | billingAccounts / invoiceSections / productMoveOperations | Nem | Nem |
> | billingAccounts/invoiceSections/termékek | Nem | Nem |
> | billingAccounts/invoiceSections/termékek/átvitel | Nem | Nem |
> | billingAccounts/invoiceSections/termékek/updateAutoRenew | Nem | Nem |
> | billingAccounts/invoiceSections/tranzakciók | Nem | Nem |
> | billingAccounts/invoiceSections/átvitel | Nem | Nem |
> | billingAccounts / lineOfCredit | Nem | Nem |
> | billingAccounts / patchOperations | Nem | Nem |
> | billingAccounts / paymentMethods | Nem | Nem |
> | billingAccounts/termékek | Nem | Nem |
> | billingAccounts/foglalások | Nem | Nem |
> | billingAccounts/tranzakciók | Nem | Nem |
> | billingPeriods | Nem | Nem |
> | billingPermissions | Nem | Nem |
> | billingProperty | Nem | Nem |
> | billingRoleAssignments | Nem | Nem |
> | billingRoleDefinitions | Nem | Nem |
> | createBillingRoleAssignment | Nem | Nem |
> | részlegek | Nem | Nem |
> | enrollmentAccounts | Nem | Nem |
> | számlák | Nem | Nem |
> | transzferek | Nem | Nem |
> | átvitelek/acceptTransfer | Nem | Nem |
> | átvitelek/declineTransfer | Nem | Nem |
> | átvitelek/operationStatus | Nem | Nem |
> | átvitelek/validateTransfer | Nem | Nem |
> | validateAddress | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | mapApis | Igen | Yes |
> | updateCommunicationPreference | Nem | Nem |

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Igen | Yes |
> | cordaMembers | Igen | Yes |
> | Watchers | Igen | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | TokenServices | Igen | Yes |
> | TokenServices / BlockchainNetworks | Nem | Nem |
> | TokenServices/csoportok | Nem | Nem |
> | TokenServices/csoportok/fiókok | Nem | Nem |
> | TokenServices / TokenTemplates | Nem | Nem |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nem | Nem |
> | blueprintAssignments / assignmentOperations | Nem | Nem |
> | blueprintAssignments/műveletek | Nem | Nem |
> | tervrajzok | Nem | Nem |
> | tervrajzok/összetevők | Nem | Nem |
> | tervezetek/verziók | Nem | Nem |
> | tervrajzok/verziók/összetevők | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | botServices | Igen | Yes |
> | botServices/csatornák | Nem | Nem |
> | botServices/kapcsolatok | Nem | Nem |
> | nyelv | Nem | Nem |
> | sablonok | Nem | Nem |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Redis | Igen | Yes |
> | Redis/EventGridFilters | Nem | Nem |
> | Redis/privateEndpointConnectionProxies | Nem | Nem |
> | Redis/privateEndpointConnectionProxies/érvényesítés | Nem | Nem |
> | Redis/privateEndpointConnections | Nem | Nem |
> | Redis/privateLinkResources | Nem | Nem |
> | redisEnterprise | Igen | Yes |
> | RedisEnterprise / privateEndpointConnectionProxies | Nem | Nem |
> | RedisEnterprise/privateEndpointConnectionProxies/érvényesítés | Nem | Nem |
> | RedisEnterprise / privateEndpointConnections | Nem | Nem |
> | RedisEnterprise / privateLinkResources | Nem | Nem |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nem | Nem |
> | autoQuotaIncrease | Nem | Nem |
> | calculateExchange | Nem | Nem |
> | calculatePrice | Nem | Nem |
> | calculatePurchasePrice | Nem | Nem |
> | katalógusok | Nem | Nem |
> | commercialReservationOrders | Nem | Nem |
> | Exchange | Nem | Nem |
> | ownReservations | Nem | Nem |
> | placePurchaseOrder | Nem | Nem |
> | reservationOrders | Nem | Nem |
> | reservationOrders / calculateRefund | Nem | Nem |
> | reservationOrders/egyesítés | Nem | Nem |
> | reservationOrders/foglalások | Nem | Nem |
> | reservationOrders/foglalások/változatok | Nem | Nem |
> | reservationOrders/Return | Nem | Nem |
> | reservationOrders/felosztás | Nem | Nem |
> | reservationOrders/swap | Nem | Nem |
> | foglalások | Nem | Nem |
> | resourceProviders | Nem | Nem |
> | resources | Nem | Nem |
> | validateReservationOrder | Nem | Nem |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nem | Nem |
> | CdnWebApplicationFirewallPolicies | Igen | Yes |
> | edgenodes | Nem | Nem |
> | profilok | Igen | Yes |
> | profilok/végpontok | Igen | Yes |
> | profilok/végpontok/customdomains | Nem | Nem |
> | profilok/végpontok/origingroups | Nem | Nem |
> | profilok/végpontok/eredetek | Nem | Nem |
> | validateProbe | Nem | Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Tanúsítványrendelések | Igen | Yes |
> | Tanúsítványrendelések/tanúsítványok | Nem | Nem |
> | validateCertificateRegistrationInformation | Nem | Nem |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | profil | Nem | Nem |
> | resourceChanges | Nem | Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | képességek | Nem | Nem |
> | Tartománynevek | Nem | Nem |
> | Tartománynevek/képességek | Nem | Nem |
> | Tartománynevek/internalLoadBalancers | Nem | Nem |
> | Tartománynevek/serviceCertificates | Nem | Nem |
> | Tartománynevek/tárolóhelyek | Nem | Nem |
> | Tartománynevek/bővítőhelyek/szerepkörök | Nem | Nem |
> | Tartománynevek/bővítőhelyek/szerepkörök/metricDefinitions | Nem | Nem |
> | Tartománynevek/bővítőhelyek/szerepkörök/mérőszámok | Nem | Nem |
> | moveSubscriptionResources | Nem | Nem |
> | operatingSystemFamilies | Nem | Nem |
> | operatingSystems | Nem | Nem |
> | kvóták | Nem | Nem |
> | resourceTypes | Nem | Nem |
> | validateSubscriptionMoveAvailability | Nem | Nem |
> | virtualMachines | Nem | Nem |
> | virtualMachines / diagnosticSettings | Nem | Nem |
> | virtualMachines / metricDefinitions | Nem | Nem |
> | virtualMachines/mérőszámok | Nem | Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nem | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | képességek | Nem | Nem |
> | expressRouteCrossConnections | Nem | Nem |
> | expressRouteCrossConnections/társak | Nem | Nem |
> | gatewaySupportedDevices | Nem | Nem |
> | networkSecurityGroups | Nem | Nem |
> | kvóták | Nem | Nem |
> | reservedIps | Nem | Nem |
> | virtualNetworks | Nem | Nem |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nem | Nem |
> | virtualNetworks/virtualNetworkPeerings | Nem | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | képességek | Nem | Nem |
> | lemezek | Nem | Nem |
> | images | Nem | Nem |
> | osImages | Nem | Nem |
> | osPlatformImages | Nem | Nem |
> | publicImages | Nem | Nem |
> | kvóták | Nem | Nem |
> | storageAccounts | Nem | Nem |
> | storageAccounts/blobServices | Nem | Nem |
> | storageAccounts/fileServices | Nem | Nem |
> | storageAccounts/metricDefinitions | Nem | Nem |
> | storageAccounts/mérőszámok | Nem | Nem |
> | storageAccounts/queueServices | Nem | Nem |
> | storageAccounts/szolgáltatások | Nem | Nem |
> | storageAccounts/szolgáltatások/diagnosticSettings | Nem | Nem |
> | storageAccounts/szolgáltatások/metricDefinitions | Nem | Nem |
> | storageAccounts/szolgáltatások/mérőszámok | Nem | Nem |
> | storageAccounts/tableServices | Nem | Nem |
> | storageAccounts/lemezképet | Nem | Nem |
> | Lemezképet | Nem | Nem |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | tervek | Igen | Nem |
> | registeredSubscriptions | Nem | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | fiókok/privateEndpointConnectionProxies | Nem | Nem |
> | fiókok/privateEndpointConnections | Nem | Nem |
> | fiókok/privateLinkResources | Nem | Nem |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | RateCard | Nem | Nem |
> | UsageAggregates | Nem | Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Igen | Yes |
> | cloudServices | Igen | Yes |
> | cloudServices / networkInterfaces | Nem | Nem |
> | cloudServices/nyilvános IP | Nem | Nem |
> | cloudServices / roleInstances | Nem | Nem |
> | cloudServices / roleInstances / networkInterfaces | Nem | Nem |
> | cloudServices/szerepkörök | Nem | Nem |
> | diskAccesses | Igen | Yes |
> | diskEncryptionSets | Igen | Yes |
> | lemezek | Igen | Yes |
> | katalógusok | Igen | Yes |
> | galériák/alkalmazások | Nem | Nem |
> | galériák/alkalmazások/verziók | Nem | Nem |
> | galériák/lemezképek | Nem | Nem |
> | galériák/lemezképek/verziók | Nem | Nem |
> | hostGroups | Igen | Yes |
> | hostGroups/gazdagépek | Igen | Yes |
> | images | Igen | Yes |
> | proximityPlacementGroups | Igen | Yes |
> | restorePointCollections | Igen | Yes |
> | restorePointCollections / restorePoints | Nem | Nem |
> | sharedVMExtensions | Igen | Yes |
> | sharedVMExtensions/verziók | Nem | Nem |
> | sharedVMImages | Igen | Yes |
> | sharedVMImages/verziók | Nem | Nem |
> | pillanatképek | Igen | Yes |
> | sshPublicKeys | Igen | Yes |
> | virtualMachines | Igen | Yes |
> | virtualMachines/bővítmények | Igen | Yes |
> | virtualMachines / metricDefinitions | Nem | Nem |
> | virtualMachines / runCommands | Igen | Yes |
> | virtualMachineScaleSets | Igen | Yes |
> | virtualMachineScaleSets/bővítmények | Nem | Nem |
> | virtualMachineScaleSets/networkInterfaces | Nem | Nem |
> | virtualMachineScaleSets/nyilvános IP | Igen | Nem |
> | virtualMachineScaleSets/virtualMachines | Nem | Nem |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nem | Nem |

> [!NOTE]
> Nem adhat hozzá olyan címkét egy virtuális géphez, amelyet általánosított jelölt meg. A virtuális gépeket a [set-AzVm-általánosított](/powershell/module/Az.Compute/Set-AzVM) vagy [az VM általánosítással](/cli/azure/vm#az-vm-generalize)általánosítva jelölheti meg.

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Igen | Yes |

## <a name="microsoftconsumption"></a>Microsoft. felhasználás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nem | Nem |
> | Egyenlegek | Nem | Nem |
> | Költségvetések | Nem | Nem |
> | Díjak | Nem | Nem |
> | CostTags | Nem | Nem |
> | hitelek | Nem | Nem |
> | események | Nem | Nem |
> | Előrejelzések | Nem | Nem |
> | számos | Nem | Nem |
> | Piacterek | Nem | Nem |
> | Árlisták | Nem | Nem |
> | termékek | Nem | Nem |
> | ReservationDetails | Nem | Nem |
> | ReservationRecommendationDetails | Nem | Nem |
> | ReservationRecommendations | Nem | Nem |
> | ReservationSummaries | Nem | Nem |
> | ReservationTransactions | Nem | Nem |
> | Címkék | Nem | Nem |
> | bérlők | Nem | Nem |
> | Kifejezések | Nem | Nem |
> | UsageDetails | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | containerGroups | Igen | Yes |
> | serviceAssociationLinks | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kibocsátásiegység | Igen | Yes |
> | kibocsátásiegység-forgalmi jegyzékek/agentPools | Igen | Yes |
> | kibocsátásiegység-forgalmi jegyzékek/buildek | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildek/Mégse | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildek/getLogLink | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildTasks | Igen | Yes |
> | kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/eventGridFilters | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/exportPipelines | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/generateCredentials | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/getBuildSourceUploadUrl | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/GetCredentials | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/importImage | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/importPipelines | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/pipelineRuns | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/privateEndpointConnectionProxies | Nem | Nem |
> | nyilvántartások/privateEndpointConnectionProxies/érvényesítés | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/privateEndpointConnections | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/privateLinkResources | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/queueBuild | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/regenerateCredential | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/regenerateCredentials | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen | Yes |
> | kibocsátásiegység-forgalmi jegyzékek/futtatások | Nem | Nem |
> | nyilvántartások/futtatások/megszakítás | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/scheduleRun | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/scopeMaps | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/taskRuns | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | Igen | Yes |
> | kibocsátásiegység-forgalmi jegyzékek/jogkivonatok | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/updatePolicies | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | Igen | Yes |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/getCallbackConfig | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/ping | Nem | Nem |

## <a name="microsoftcontainerservice"></a>Microsoft. Tárolószolgáltatás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | containerServices | Igen | Yes |
> | managedClusters | Igen | Yes |
> | openShiftManagedClusters | Igen | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Riasztások | Nem | Nem |
> | BillingAccounts | Nem | Nem |
> | Költségvetések | Nem | Nem |
> | CloudConnectors | Nem | Nem |
> | Összekötők | Igen | Yes |
> | costAllocationRules | Nem | Nem |
> | Részlegek | Nem | Nem |
> | Dimenziók | Nem | Nem |
> | EnrollmentAccounts | Nem | Nem |
> | Exportálások | Nem | Nem |
> | ExternalBillingAccounts | Nem | Nem |
> | ExternalBillingAccounts/riasztások | Nem | Nem |
> | ExternalBillingAccounts/méretek | Nem | Nem |
> | ExternalBillingAccounts/előrejelzés | Nem | Nem |
> | ExternalBillingAccounts/lekérdezés | Nem | Nem |
> | ExternalSubscriptions | Nem | Nem |
> | ExternalSubscriptions/riasztások | Nem | Nem |
> | ExternalSubscriptions/méretek | Nem | Nem |
> | ExternalSubscriptions/előrejelzés | Nem | Nem |
> | ExternalSubscriptions/lekérdezés | Nem | Nem |
> | Előrejelzés | Nem | Nem |
> | Insights | Nem | Nem |
> | Lekérdezés | Nem | Nem |
> | Regisztráció | Nem | Nem |
> | Reportconfigs | Nem | Nem |
> | Jelentések | Nem | Nem |
> | Beállítások | Nem | Nem |
> | showbackRules | Nem | Nem |
> | Nézetek | Nem | Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kérelmek | Nem | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | szövetségek | Nem | Nem |
> | resourceProviders | Igen | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | esetben | Igen | Yes |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | feladatok | Igen | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Igen | Yes |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | munkaterületek | Igen | Yes |
> | munkaterületek/dbWorkspaces | Nem | Nem |
> | munkaterületek/virtualNetworkPeerings | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | katalógusok | Igen | Yes |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | dataFactories | Igen | Yes |
> | dataFactories / diagnosticSettings | Nem | Nem |
> | dataFactories / metricDefinitions | Nem | Nem |
> | dataFactorySchema | Nem | Nem |
> | előállítók | Igen | Yes |
> | gyárak/integrationRuntimes | Nem | Nem |

> [!NOTE]
> Ha az Azure-SSIS integrációs modulokkal rendelkezik a saját adatgyárában, a futó költségeket a rendszer a Factory-címkék címkével látja el. Az Azure-SSIS integrációs moduljának futtatásához le kell állítani és újra kell indítani az új adatelőállítói címkéket, hogy azok üzemeltetési költségeit alkalmazni lehessen.

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | fiókok/dataLakeStoreAccounts | Nem | Nem |
> | fiókok/storageAccounts | Nem | Nem |
> | fiókok/storageAccounts/tárolók | Nem | Nem |
> | fiókok/transferAnalyticsUnits | Nem | Nem |

## <a name="microsoftdatalakestore"></a>Microsoft. Data Lake Store

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | fiókok/eventGridFilters | Nem | Nem |
> | fiókok/firewallRules | Nem | Nem |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | services | Nem | Nem |
> | szolgáltatások/projektek | Nem | Nem |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Igen | Yes |
> | ResourceOperationGateKeepers | Igen | Yes |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | fiókok/megosztások | Nem | Nem |
> | fiókok/megosztások/adatkészletek | Nem | Nem |
> | fiókok/megosztások/meghívók | Nem | Nem |
> | fiókok/megosztások/providersharesubscriptions | Nem | Nem |
> | fiókok/megosztások/synchronizationSettings | Nem | Nem |
> | fiókok/sharesubscriptions | Nem | Nem |
> | fiókok/sharesubscriptions/consumerSourceDataSets | Nem | Nem |
> | fiókok/sharesubscriptions/datasetmappings | Nem | Nem |
> | fiókok/sharesubscriptions/eseményindítók | Nem | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kiszolgálók | Igen | Yes |
> | kiszolgálók/tanácsadók | Nem | Nem |
> | kiszolgálók/kulcsok | Nem | Nem |
> | kiszolgálók/privateEndpointConnectionProxies | Nem | Nem |
> | kiszolgálók/privateEndpointConnections | Nem | Nem |
> | kiszolgálók/privateLinkResources | Nem | Nem |
> | kiszolgálók/queryTexts | Nem | Nem |
> | kiszolgálók/recoverableServers | Nem | Nem |
> | kiszolgálók/indítás | Nem | Nem |
> | kiszolgálók/leállítás | Nem | Nem |
> | kiszolgálók/topQueryStatistics | Nem | Nem |
> | kiszolgálók/virtualNetworkRules | Nem | Nem |
> | kiszolgálók/waitStatistics | Nem | Nem |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Igen | Yes |
> | kiszolgálók | Igen | Yes |
> | kiszolgálók/tanácsadók | Nem | Nem |
> | kiszolgálók/kulcsok | Nem | Nem |
> | kiszolgálók/privateEndpointConnectionProxies | Nem | Nem |
> | kiszolgálók/privateEndpointConnections | Nem | Nem |
> | kiszolgálók/privateLinkResources | Nem | Nem |
> | kiszolgálók/queryTexts | Nem | Nem |
> | kiszolgálók/recoverableServers | Nem | Nem |
> | kiszolgálók/indítás | Nem | Nem |
> | kiszolgálók/leállítás | Nem | Nem |
> | kiszolgálók/topQueryStatistics | Nem | Nem |
> | kiszolgálók/verziófrissítés | Nem | Nem |
> | kiszolgálók/virtualNetworkRules | Nem | Nem |
> | kiszolgálók/waitStatistics | Nem | Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Igen | Yes |
> | serverGroups | Igen | Yes |
> | kiszolgálók | Igen | Yes |
> | kiszolgálók/tanácsadók | Nem | Nem |
> | kiszolgálók/kulcsok | Nem | Nem |
> | kiszolgálók/privateEndpointConnectionProxies | Nem | Nem |
> | kiszolgálók/privateEndpointConnections | Nem | Nem |
> | kiszolgálók/privateLinkResources | Nem | Nem |
> | kiszolgálók/queryTexts | Nem | Nem |
> | kiszolgálók/recoverableServers | Nem | Nem |
> | kiszolgálók/topQueryStatistics | Nem | Nem |
> | kiszolgálók/virtualNetworkRules | Nem | Nem |
> | kiszolgálók/waitStatistics | Nem | Nem |
> | serversv2 | Igen | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | artifactSources | Igen | Yes |
> | kibocsátások | Igen | Yes |
> | serviceTopologies | Igen | Yes |
> | serviceTopologies/szolgáltatások | Igen | Yes |
> | serviceTopologies/szolgáltatások/serviceUnits | Igen | Yes |
> | lépések | Igen | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Igen | Yes |
> | applicationgroups/alkalmazások | Nem | Nem |
> | applicationgroups/asztali számítógépek | Nem | Nem |
> | applicationgroups / startmenuitems | Nem | Nem |
> | hostpools | Igen | Yes |
> | hostpools / msixpackages | Nem | Nem |
> | hostpools / sessionhosts | Nem | Nem |
> | hostpools / sessionhosts / usersessions | Nem | Nem |
> | hostpools / usersessions | Nem | Nem |
> | munkaterületek | Igen | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Igen | Yes |
> | ElasticPools / IotHubTenants | Igen | Yes |
> | ElasticPools/IotHubTenants/securitySettings | Nem | Nem |
> | IotHubs | Igen | Yes |
> | IotHubs/eventGridFilters | Nem | Nem |
> | IotHubs/securitySettings | Nem | Nem |
> | ProvisioningServices | Igen | Yes |
> | használat | Nem | Nem |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | fiókok/példányok | Igen | Yes |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | csővezetékek | Igen | Yes |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | tartományvezérlők | Igen | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | labcenters | Igen | Yes |
> | Labs | Igen | Yes |
> | Labs/környezetek | Igen | Yes |
> | Labor/serviceRunners | Igen | Yes |
> | Labor/virtualMachines | Igen | Yes |
> | menetrend | Igen | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Igen | Yes |
> | digitalTwinsInstances/végpontok | Nem | Nem |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nem | Nem |
> | databaseAccounts | Igen | Yes |
> | restorableDatabaseAccounts | Nem | Nem |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | tartományok | Igen | Yes |
> | tartományok/domainOwnershipIdentifiers | Nem | Nem |
> | generateSsoRequest | Nem | Nem |
> | topLevelDomains | Nem | Nem |
> | validateDomainRegistrationInformation | Nem | Nem |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nem | Nem |
> | lcsprojects / clouddeployments | Nem | Nem |
> | lcsprojects/összekötők | Nem | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | services | Igen | Yes |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | tartományok | Igen | Yes |
> | tartományok/témakörök | Nem | Nem |
> | eventSubscriptions | Nem | Nem |
> | extensionTopics | Nem | Nem |
> | partnerNamespaces | Igen | Yes |
> | partnerNamespaces/eventChannels | Nem | Nem |
> | partnerRegistrations | Igen | Yes |
> | partnerTopics | Igen | Yes |
> | partnerTopics / eventSubscriptions | Nem | Nem |
> | systemTopics | Igen | Yes |
> | systemTopics / eventSubscriptions | Nem | Nem |
> | témakörök | Igen | Yes |
> | topicTypes | Nem | Nem |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fürtök | Igen | Yes |
> | névterek | Igen | Yes |
> | névterek/engedélyezési szabályok | Nem | Nem |
> | névterek/disasterrecoveryconfigs | Nem | Nem |
> | névterek/eventhubs | Nem | Nem |
> | névterek/eventhubs/engedélyezési szabályok | Nem | Nem |
> | névterek/eventhubs/consumergroups | Nem | Nem |
> | névterek/networkrulesets | Nem | Nem |
> | névterek/privateEndpointConnections | Nem | Nem |

## <a name="microsoftexperimentation"></a>Microsoft. kísérletezés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Igen | Yes |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | névterek | Igen | Yes |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | featureProviders | Nem | Nem |
> | funkciók | Nem | Nem |
> | szolgáltatók | Nem | Nem |
> | subscriptionFeatureRegistrations | Nem | Nem |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | beiratkozik | Nem | Nem |
> | galleryitems | Nem | Nem |
> | generateartifactaccessuri | Nem | Nem |
> | myareas | Nem | Nem |
> | myareas/területek | Nem | Nem |
> | myareas/területek/területek | Nem | Nem |
> | myareas/területek/területek/galleryitems | Nem | Nem |
> | myareas/területek/galleryitems | Nem | Nem |
> | myareas / galleryitems | Nem | Nem |
> | Regisztráció | Nem | Nem |
> | resources | Nem | Nem |
> | retrieveresourcesbyid | Nem | Nem |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Igen | Yes |
> | autoManagedVmConfigurationProfiles | Igen | Yes |
> | configurationProfileAssignments | Nem | Nem |
> | guestConfigurationAssignments | Nem | Nem |
> | szoftver | Nem | Nem |
> | softwareUpdateProfile | Nem | Nem |
> | softwareUpdates | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Igen | Yes |
> | sapMonitors | Igen | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Igen | Yes |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fürtök | Igen | Yes |
> | fürtök/alkalmazások | Nem | Nem |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | services | Igen | Yes |
> | szolgáltatások/iomtconnectors | Nem | Nem |
> | szolgáltatások/iomtconnectors/kapcsolatok | Nem | Nem |
> | szolgáltatások/iomtconnectors/leképezések | Nem | Nem |
> | szolgáltatások/privateEndpointConnectionProxies | Nem | Nem |
> | szolgáltatások/privateEndpointConnections | Nem | Nem |
> | szolgáltatások/privateLinkResources | Nem | Nem |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | gépek | Igen | Yes |
> | gépek/assessPatches | Nem | Nem |
> | gépek/bővítmények | Igen | Yes |
> | gépek/installPatches | Nem | Nem |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | dataManagers | Igen | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | eszközök | Igen | Yes |
> | networkFunctions | Igen | Yes |
> | networkFunctionVendors | Nem | Nem |
> | registeredSubscriptions | Nem | Nem |
> | szállítók | Nem | Nem |
> | szállítók/vendorSkus | Nem | Nem |
> | szállítók/vendorSkus/previewSubscriptions | Nem | Nem |
> | virtualNetworkFunctions | Igen | Yes |
> | virtualNetworkFunctionVendors | Nem | Nem |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | összetevők | Igen | Yes |
> | networkScopes | Igen | Yes |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | feladatok | Igen | Yes |

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | actionGroups | Igen | Yes |
> | activityLogAlerts | Igen | Yes |
> | alertrules | Igen | Yes |
> | autoscalesettings | Igen | Yes |
> | összetevők | Igen | Yes |
> | összetevők/linkedStorageAccounts | Nem | Nem |
> | összetevők/ProactiveDetectionConfigs | Nem | Nem |
> | diagnosticSettings | Nem | Nem |
> | guestDiagnosticSettings | Igen | Yes |
> | guestDiagnosticSettingsAssociation | Igen | Yes |
> | logprofiles | Igen | Yes |
> | metricAlerts | Igen | Yes |
> | privateLinkScopes | Igen | Yes |
> | privateLinkScopes / privateEndpointConnections | Nem | Nem |
> | privateLinkScopes / scopedResources | Nem | Nem |
> | queryPacks | Igen | Yes |
> | queryPacks/lekérdezések | Nem | Nem |
> | scheduledQueryRules | Igen | Yes |
> | webteszteket | Igen | Yes |
> | munkafüzetek | Igen | Yes |
> | workbooktemplates | Igen | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nem | Nem |
> | diagnosticSettingsCategories | Nem | Nem |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nem | Nem |
> | IoTApps | Igen | Yes |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Graph | Igen | Yes |

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nem | Nem |
> | hsmPools | Igen | Yes |
> | managedHSMs | Igen | Yes |
> | boltívek | Igen | Yes |
> | tárolók/accessPolicies | Nem | Nem |
> | tárolók/eventGridFilters | Nem | Nem |
> | tárolók/kulcsok | Nem | Nem |
> | tárolók/kulcsok/verziók | Nem | Nem |
> | tárolók/titkok | Nem | Nem |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Igen | Yes |
> | registeredSubscriptions | Nem | Nem |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Extensions | Nem | Nem |
> | sourceControlConfigurations | Nem | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fürtök | Igen | Yes |
> | fürtök/attacheddatabaseconfigurations | Nem | Nem |
> | fürtök/adatbázisok | Nem | Nem |
> | fürtök/adatbázisok/dataconnections | Nem | Nem |
> | fürtök/adatbázisok/eventhubconnections | Nem | Nem |
> | fürtök/adatbázisok/principalassignments | Nem | Nem |
> | fürtök/dataconnections | Nem | Nem |
> | fürtök/principalassignments | Nem | Nem |
> | fürtök/sharedidentities | Nem | Nem |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | labaccounts | Igen | Nem |
> | felhasználók | Nem | Nem |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Igen | Yes |
> | integrationAccounts | Igen | Yes |
> | integrationServiceEnvironments | Igen | Yes |
> | integrationServiceEnvironments/król | Nem | Nem |
> | isolatedEnvironments | Igen | Yes |
> | munkafolyamatok | Igen | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Igen | Yes |
> | webServices | Igen | Yes |
> | Munkaterületek | Igen | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | munkaterületek | Igen | Yes |
> | munkaterületek/batchEndpoints | Igen | Yes |
> | munkaterületek/batchEndpoints/központi telepítések | Igen | Yes |
> | munkaterületek/kódok | Nem | Nem |
> | munkaterületek/kódok/verziók | Nem | Nem |
> | munkaterületek/számítások | Nem | Nem |
> | munkaterületek/adattárolók | Nem | Nem |
> | munkaterületek/eventGridFilters | Nem | Nem |
> | munkaterületek/feladatok | Nem | Nem |
> | munkaterületek/labelingJobs | Nem | Nem |
> | munkaterületek/linkedServices | Nem | Nem |
> | munkaterületek/modellek | Nem | Nem |
> | munkaterületek/modellek/verziók | Nem | Nem |
> | munkaterületek/onlineEndpoints | Igen | Yes |
> | munkaterületek/onlineEndpoints/központi telepítések | Igen | Yes |
 
> [!NOTE]
> A munkaterület-címkék nem terjednek ki a számítási fürtökre és a számítási példányokra. 

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Nem | Nem |
> | configurationAssignments | Nem | Nem |
> | maintenanceConfigurations | Igen | Yes |
> | publicMaintenanceConfigurations | Nem | Nem |
> | frissítések | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Identitások | Nem | Nem |
> | userAssignedIdentities | Igen | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Igen | Yes |
> | managedNetworks / managedNetworkGroups | Igen | Yes |
> | managedNetworks / managedNetworkPeeringPolicies | Igen | Yes |
> | értesítés | Igen | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nem | Nem |
> | registrationAssignments | Nem | Nem |
> | registrationDefinitions | Nem | Nem |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | getEntities | Nem | Nem |
> | managementGroups | Nem | Nem |
> | managementGroups/beállítások | Nem | Nem |
> | resources | Nem | Nem |
> | startTenantBackfill | Nem | Nem |
> | tenantBackfillStatus | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | fiókok/eventGridFilters | Nem | Nem |
> | fiókok/privateAtlases | Igen | Yes |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | MACC | Nem | Nem |
> | kínál | Nem | Nem |
> | offerTypes | Nem | Nem |
> | offerTypes/közzétevők | Nem | Nem |
> | offerTypes/kiadók/ajánlatok | Nem | Nem |
> | offerTypes/kiadók/ajánlatok/csomagok | Nem | Nem |
> | offerTypes/kiadók/ajánlatok/csomagok/szerződések | Nem | Nem |
> | offerTypes/kiadók/ajánlatok/csomagok/konfigurációk | Nem | Nem |
> | offerTypes/kiadók/ajánlatok/csomagok/konfigurációk/importImage | Nem | Nem |
> | privategalleryitems | Nem | Nem |
> | privateStoreClient | Nem | Nem |
> | privateStores | Nem | Nem |
> | privateStores/ajánlatok | Nem | Nem |
> | termékek | Nem | Nem |
> | közzétevők | Nem | Nem |
> | kiadók/ajánlatok | Nem | Nem |
> | közzétevők/ajánlatok/módosítások | Nem | Nem |
> | Regisztráció | Nem | Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Igen | Yes |
> | updateCommunicationPreference | Nem | Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | megállapodások | Nem | Nem |
> | offertypes | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Mediaservices | Igen | Yes |
> | Mediaservices/accountFilters | Nem | Nem |
> | Mediaservices/-eszközök | Nem | Nem |
> | Mediaservices/eszközök/assetFilters | Nem | Nem |
> | Mediaservices/contentKeyPolicies | Nem | Nem |
> | Mediaservices/eventGridFilters | Nem | Nem |
> | Mediaservices/liveEventOperations | Nem | Nem |
> | Mediaservices/liveEvents | Igen | Yes |
> | Mediaservices/liveEvents/liveOutputs | Nem | Nem |
> | Mediaservices/liveOutputOperations | Nem | Nem |
> | Mediaservices/mediaGraphs | Nem | Nem |
> | Mediaservices/privateEndpointConnectionOperations | Nem | Nem |
> | Mediaservices/privateEndpointConnectionProxies | Nem | Nem |
> | Mediaservices/privateEndpointConnections | Nem | Nem |
> | Mediaservices/streamingEndpointOperations | Nem | Nem |
> | Mediaservices/streamingEndpoints | Igen | Yes |
> | Mediaservices/streamingLocators | Nem | Nem |
> | Mediaservices/streamingPolicies | Nem | Nem |
> | Mediaservices/átalakítások | Nem | Nem |
> | Mediaservices/átalakítások/feladatok | Nem | Nem |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | appClusters | Igen | Yes |

## <a name="microsoftmigrate"></a>Microsoft. Migrálás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Igen | Yes |
> | migrateprojects | Igen | Yes |
> | moveCollections | Igen | Yes |
> | projektek | Igen | Yes |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Igen | Yes |
> | objectUnderstandingAccounts | Igen | Yes |
> | remoteRenderingAccounts | Igen | Yes |
> | spatialAnchorsAccounts | Igen | Yes |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Igen | Nem |
> | netAppAccounts / accountBackups | Nem | Nem |
> | netAppAccounts / capacityPools | Igen | Nem |
> | netAppAccounts/capacityPools/kötetek | Igen | Nem |
> | netAppAccounts/capacityPools/kötetek/Pillanatképek | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Igen | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Igen | Yes |
> | applicationSecurityGroups | Igen | Yes |
> | azureFirewallFqdnTags | Nem | Nem |
> | azureFirewalls | Igen | Nem |
> | bastionHosts | Igen | Nem |
> | bgpServiceCommunities | Nem | Nem |
> | kapcsolatok | Igen | Yes |
> | ddosCustomPolicies | Igen | Yes |
> | ddosProtectionPlans | Igen | Yes |
> | dnsOperationStatuses | Nem | Nem |
> | dnszones | Igen | Yes |
> | dnszones/A | Nem | Nem |
> | dnszones/AAAA | Nem | Nem |
> | dnszones/mind | Nem | Nem |
> | dnszones/CAA | Nem | Nem |
> | dnszones/CNAME | Nem | Nem |
> | dnszones/MX | Nem | Nem |
> | dnszones/NS | Nem | Nem |
> | dnszones/PTR | Nem | Nem |
> | dnszones/rekordhalmazok | Nem | Nem |
> | dnszones/SOA | Nem | Nem |
> | dnszones/SRV | Nem | Nem |
> | dnszones/TXT | Nem | Nem |
> | expressRouteCircuits | Igen | Yes |
> | expressRouteCrossConnections | Igen | Yes |
> | expressRouteGateways | Igen | Yes |
> | expressRoutePorts | Igen | Yes |
> | expressRouteServiceProviders | Nem | Nem |
> | firewallPolicies | Igen | Yes |
> | frontdoors | Igen, de korlátozott (lásd az [alábbi megjegyzést](#frontdoor)) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Igen, de korlátozott (lásd az [alábbi megjegyzést](#frontdoor)) | No |
> | frontdoorWebApplicationFirewallPolicies | Igen, de korlátozott (lásd az [alábbi megjegyzést](#frontdoor)) | Yes |
> | getDnsResourceReference | Nem | Nem |
> | internalNotify | Nem | Nem |
> | ipGroups | Igen | Yes |
> | loadBalancers | Igen | Yes |
> | localNetworkGateways | Igen | Yes |
> | natGateways | Igen | Yes |
> | networkIntentPolicies | Igen | Yes |
> | networkInterfaces | Igen | Yes |
> | networkProfiles | Igen | Yes |
> | networkSecurityGroups | Igen | Yes |
> | networkWatchers | Igen | Yes |
> | networkWatchers / connectionMonitors | Igen | Nem |
> | networkWatchers / flowLogs | Nem | Nem |
> | networkWatchers/objektívek | Igen | Nem |
> | networkWatchers / pingMeshes | Igen | Nem |
> | p2sVpnGateways | Igen | Yes |
> | privateDnsOperationStatuses | Nem | Nem |
> | privateDnsZones | Igen | Yes |
> | privateDnsZones/A | Nem | Nem |
> | privateDnsZones/AAAA | Nem | Nem |
> | privateDnsZones/mind | Nem | Nem |
> | privateDnsZones/CNAME | Nem | Nem |
> | privateDnsZones/MX | Nem | Nem |
> | privateDnsZones/PTR | Nem | Nem |
> | privateDnsZones/SOA | Nem | Nem |
> | privateDnsZones/SRV | Nem | Nem |
> | privateDnsZones/TXT | Nem | Nem |
> | privateDnsZones / virtualNetworkLinks | Igen | Yes |
> | privateEndpoints | Igen | Yes |
> | privateLinkServices | Igen | Yes |
> | Nyilvános IP | Igen | Yes |
> | publicIPPrefixes | Igen | Yes |
> | routeFilters | Igen | Yes |
> | routeTables | Igen | Yes |
> | serviceEndpointPolicies | Igen | Yes |
> | trafficManagerGeographicHierarchies | Nem | Nem |
> | trafficmanagerprofiles | Igen | Yes |
> | trafficmanagerprofiles/Intenzitástérképei | Nem | Nem |
> | trafficManagerUserMetricsKeys | Nem | Nem |
> | virtualHubs | Igen | Yes |
> | virtualNetworkGateways | Igen | Yes |
> | virtualNetworks | Igen | Yes |
> | virtualNetworks/alhálózatok | Nem | Nem |
> | virtualNetworkTaps | Igen | Yes |
> | virtualWans | Igen | Nem |
> | vpnGateways | Igen | Yes |
> | vpnSites | Igen | Yes |
> | webApplicationFirewallPolicies | Igen | Yes |

<a id="frontdoor"></a>

> [!NOTE]
> Az Azure bejárati szolgáltatásához címkéket alkalmazhat az erőforrás létrehozásakor, de a címkék frissítése vagy hozzáadása jelenleg nem támogatott.


## <a name="microsoftnotebooks"></a>Microsoft. notebookok

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Nem | Nem |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | névterek | Igen | Nem |
> | névterek/notificationHubs | Igen | Nem |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Igen | Yes |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Igen | Yes |
> | ImportSites | Igen | Yes |
> | MasterSites | Igen | Yes |
> | ServerSites | Igen | Yes |
> | VMwareSites | Igen | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fürtök | Igen | Yes |
> | deletedWorkspaces | Nem | Nem |
> | linkTargets | Nem | Nem |
> | storageInsightConfigs | Nem | Nem |
> | munkaterületek | Igen | Yes |
> | munkaterületek/dataExports | Nem | Nem |
> | munkaterületek/adatforrások | Nem | Nem |
> | munkaterületek/linkedServices | Nem | Nem |
> | munkaterületek/linkedStorageAccounts | Nem | Nem |
> | munkaterületek/metaadatok | Nem | Nem |
> | munkaterületek/lekérdezés | Nem | Nem |
> | munkaterületek/scopedPrivateLinkProxies | Nem | Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nem | Nem |
> | managementconfigurations | Igen | Yes |
> | megoldások | Igen | Yes |
> | kilátással | Igen | Yes |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nem | Nem |
> | peerAsns | Nem | Nem |
> | társviszonyok | Igen | Yes |
> | peeringServiceCountries | Nem | Nem |
> | peeringServiceProviders | Nem | Nem |
> | peeringServices | Igen | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | igazolások | Nem | Nem |
> | policyEvents | Nem | Nem |
> | policyMetadata | Nem | Nem |
> | policyStates | Nem | Nem |
> | policyTrackedResources | Nem | Nem |
> | szervizelések | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> |  -konzolok | Nem | Nem |
> | irányítópultok | Igen | Yes |
> | UserSettings | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Igen | Yes |
> | bérlők | Igen | Yes |
> | bérlők/munkaterületek | Nem | Nem |
> | workspaceCollections | Igen | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kapacitások | Igen | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | deletedAccounts | Nem | Nem |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nem | Nem |
> | providerRegistrations / defaultRollouts | Nem | Nem |
> | providerRegistrations / resourceTypeRegistrations | Nem | Nem |
> | kibocsátások | Igen | Yes |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Munkaterületek | Igen | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nem | Nem |
> | boltívek | Igen | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Igen | Yes |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | névterek | Igen | Yes |
> | névterek/engedélyezési szabályok | Nem | Nem |
> | névterek/hybridconnections | Nem | Nem |
> | névterek/hybridconnections/engedélyezési szabályok | Nem | Nem |
> | névterek/privateEndpointConnections | Nem | Nem |
> | névterek/wcfrelays | Nem | Nem |
> | névterek/wcfrelays/engedélyezési szabályok | Nem | Nem |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | lekérdezések | Igen | Yes |
> | resourceChangeDetails | Nem | Nem |
> | resourceChanges | Nem | Nem |
> | resources | Nem | Nem |
> | resourcesHistory | Nem | Nem |
> | subscriptionsStatus | Nem | Nem |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nem | Nem |
> | childAvailabilityStatuses | Nem | Nem |
> | childResources | Nem | Nem |
> | emergingissues | Nem | Nem |
> | események | Nem | Nem |
> | impactedResources | Nem | Nem |
> | metaadatok | Nem | Nem |
> | értesítések | Nem | Nem |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | calculateTemplateHash | Nem | Nem |
> | központi telepítések | Igen | Nem |
> | üzembe helyezések/műveletek | Nem | Nem |
> | deploymentScripts | Igen | Yes |
> | deploymentScripts/naplók | Nem | Nem |
> | linkek | Nem | Nem |
> | notifyResourceJobs | Nem | Nem |
> | szolgáltatók | Nem | Nem |
> | resourceGroups | Igen | Nem |
> | előfizetések | Igen | Nem |
> | templateSpecs | Igen | Yes |
> | templateSpecs/verziók | Igen | Yes |
> | bérlők | Nem | Nem |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen | Yes |
> | saasresources | Nem | Nem |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | felhők | Igen | Yes |
> | VirtualMachines | Igen | Yes |
> | VirtualMachineTemplates | Igen | Yes |
> | VirtualNetworks | Igen | Yes |
> | vmmservers | Igen | Yes |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nem | Nem |
> | searchServices | Igen | Yes |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nem | Nem |
> | advancedThreatProtectionSettings | Nem | Nem |
> | riasztások | Nem | Nem |
> | alertsSuppressionRules | Nem | Nem |
> | allowedConnections | Nem | Nem |
> | applicationWhitelistings | Nem | Nem |
> | assessmentMetadata | Nem | Nem |
> | értékelések | Nem | Nem |
> | autoDismissAlertsRules | Nem | Nem |
> | automatizálások szabványának létrehozásában | Igen | Yes |
> | AutoProvisioningSettings | Nem | Nem |
> | Felelésről | Nem | Nem |
> | összekötők | Nem | Nem |
> | dataCollectionAgents | Nem | Nem |
> | deviceSecurityGroups | Nem | Nem |
> | discoveredSecuritySolutions | Nem | Nem |
> | externalSecuritySolutions | Nem | Nem |
> | InformationProtectionPolicies | Nem | Nem |
> | iotDefenderSettings | Nem | Nem |
> | iotSecuritySolutions | Igen | Yes |
> | iotSecuritySolutions / analyticsModels | Nem | Nem |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nem | Nem |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nem | Nem |
> | iotSecuritySolutions / iotAlerts | Nem | Nem |
> | iotSecuritySolutions / iotAlertTypes | Nem | Nem |
> | iotSecuritySolutions / iotRecommendations | Nem | Nem |
> | iotSecuritySolutions / iotRecommendationTypes | Nem | Nem |
> | iotSensors | Nem | Nem |
> | jitNetworkAccessPolicies | Nem | Nem |
> | jitPolicies | Nem | Nem |
> | policies | Nem | Nem |
> | pricings | Nem | Nem |
> | regulatoryComplianceStandards | Nem | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nem | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nem | Nem |
> | secureScoreControlDefinitions | Nem | Nem |
> | secureScoreControls | Nem | Nem |
> | secureScores | Nem | Nem |
> | secureScores / secureScoreControls | Nem | Nem |
> | securityContacts | Nem | Nem |
> | securitySolutions | Nem | Nem |
> | securitySolutionsReferenceData | Nem | Nem |
> | securityStatuses | Nem | Nem |
> | securityStatusesSummaries | Nem | Nem |
> | serverVulnerabilityAssessments | Nem | Nem |
> | beállítások | Nem | Nem |
> | sqlVulnerabilityAssessments | Nem | Nem |
> | alértékelések | Nem | Nem |
> | feladatok | Nem | Nem |
> | topológiák | Nem | Nem |
> | workspaceSettings | Nem | Nem |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nem | Nem |
> | diagnosticSettingsCategories | Nem | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | összesítések | Nem | Nem |
> | alertRules | Nem | Nem |
> | alertRuleTemplates | Nem | Nem |
> | automationRules | Nem | Nem |
> | könyvjelzők | Nem | Nem |
> | esetekben | Nem | Nem |
> | dataConnectors | Nem | Nem |
> | dataConnectorsCheckRequirements | Nem | Nem |
> | szervezetek | Nem | Nem |
> | entityQueries | Nem | Nem |
> | incidensek | Nem | Nem |
> | officeConsents | Nem | Nem |
> | beállítások | Nem | Nem |
> | threatIntelligence | Nem | Nem |
> | Watchlists | Nem | Nem |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | consoleServices | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | névterek | Igen | Yes |
> | névterek/engedélyezési szabályok | Nem | Nem |
> | névterek/disasterrecoveryconfigs | Nem | Nem |
> | névterek/eventgridfilters | Nem | Nem |
> | névterek/networkrulesets | Nem | Nem |
> | névterek/privateEndpointConnections | Nem | Nem |
> | névterek/várólisták | Nem | Nem |
> | névterek/várólisták/engedélyezési szabályok | Nem | Nem |
> | névterek/témakörök | Nem | Nem |
> | névterek/témakörök/engedélyezési szabályok | Nem | Nem |
> | névterek/témakörök/előfizetések | Nem | Nem |
> | névterek/témakörök/előfizetések/szabályok | Nem | Nem |
> | premiumMessagingRegions | Nem | Nem |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen | Yes |
> | fürtök | Igen | Yes |
> | fürtök/alkalmazások | Nem | Nem |
> | containerGroups | Igen | Yes |
> | containerGroupSets | Igen | Yes |
> | edgeclusters | Igen | Yes |
> | edgeclusters/alkalmazások | Nem | Nem |
> | managedclusters | Igen | Yes |
> | managedclusters / nodetypes | Nem | Nem |
> | hálózatok | Igen | Yes |
> | secretstores | Igen | Yes |
> | secretstores/tanúsítványok | Nem | Nem |
> | secretstores/titkok | Nem | Nem |
> | volumes | Igen | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen | Yes |
> | containerGroups | Igen | Yes |
> | átjárók | Igen | Yes |
> | hálózatok | Igen | Yes |
> | titkok | Igen | Yes |
> | volumes | Igen | Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nem | Nem |
> | providerRegistrations / resourceTypeRegistrations | Nem | Nem |
> | kibocsátások | Igen | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | SignalR | Igen | Yes |
> | Jelző/eventGridFilters | Nem | Nem |

## <a name="microsoftsingularity"></a>Microsoft. szingularitás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Yes |
> | fiókok/accountQuotaPolicies | Nem | Nem |
> | fiókok/groupPolicies | Nem | Nem |
> | fiókok/feladatok | Nem | Nem |
> | fiókok/storageContainers | Nem | Nem |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Igen | Yes |
> | alkalmazások | Igen | Yes |
> | jitRequests | Igen | Yes |


## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | managedInstances | Igen | Yes |
> | managedInstances/adatbázisok | Nem | Nem |
> | managedInstances/adatbázisok/backupShortTermRetentionPolicies | Nem | Nem |
> | managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels | Nem | Nem |
> | managedInstances/adatbázisok/vulnerabilityAssessments | Nem | Nem |
> | managedInstances/adatbázisok/vulnerabilityAssessments/szabályok/alaptervek | Nem | Nem |
> | managedInstances / encryptionProtector | Nem | Nem |
> | managedInstances/kulcsok | Nem | Nem |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nem | Nem |
> | managedInstances / vulnerabilityAssessments | Nem | Nem |
> | kiszolgálók | Igen | Yes |
> | kiszolgálók/rendszergazdák | Nem | Nem |
> | kiszolgálók/communicationLinks | Nem | Nem |
> | kiszolgálók/adatbázisok | Igen (lásd az [alábbi megjegyzést](#sqlnote)) | Yes |
> | kiszolgálók/encryptionProtector | Nem | Nem |
> | kiszolgálók/firewallRules | Nem | Nem |
> | kiszolgálók/kulcsok | Nem | Nem |
> | kiszolgálók/restorableDroppedDatabases | Nem | Nem |
> | kiszolgálók/serviceobjectives | Nem | Nem |
> | kiszolgálók/tdeCertificates | Nem | Nem |
> | virtualClusters | Nem | Nem |

<a id="sqlnote"></a>

> [!NOTE]
> A főadatbázis nem támogatja a címkéket, de más adatbázisokat, köztük az Azure szinapszis Analytics-adatbázisokat, a támogatási címkéket. Az Azure szinapszis elemzési adatbázisainak aktív (nem szüneteltetett) állapotban kell lenniük.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Igen | Yes |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nem | Nem |
> | SqlVirtualMachines | Igen | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | Nem | Nem |
> | storageAccounts | Igen | Yes |
> | storageAccounts/blobServices | Nem | Nem |
> | storageAccounts/fileServices | Nem | Nem |
> | storageAccounts/queueServices | Nem | Nem |
> | storageAccounts/szolgáltatások | Nem | Nem |
> | storageAccounts/szolgáltatások/metricDefinitions | Nem | Nem |
> | storageAccounts/tableServices | Nem | Nem |
> | használat | Nem | Nem |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | gyorsítótárak | Igen | Yes |
> | gyorsítótárak/storageTargets | Nem | Nem |
> | usageModels | Nem | Nem |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nem | Nem |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Yes |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices/munkafolyamatok | Nem | Nem |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Yes |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices/munkafolyamatok | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Yes |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices/munkafolyamatok | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kezelők | Igen | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fürtök | Igen | Yes |
> | fürtök/privateEndpoints | Nem | Nem |
> | streamingjobs | Igen (lásd az alábbi megjegyzést) | Yes |

> [!NOTE]
> Nem adhat hozzá címkét, ha a streamingjobs fut. Egy címke hozzáadásához állítsa le az erőforrást.

## <a name="microsoftsubscription"></a>Microsoft. előfizetés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Nem | Nem |
> | aliasok | Nem | Nem |
> | mégse | Nem | Nem |
> | changeTenantRequest | Nem | Nem |
> | changeTenantStatus | Nem | Nem |
> | CreateSubscription | Nem | Nem |
> | engedélyezése | Nem | Nem |
> | átnevezés | Nem | Nem |
> | SubscriptionDefinitions | Nem | Nem |
> | SubscriptionOperations | Nem | Nem |
> | előfizetések | Nem | Nem |

## <a name="microsoftsynapse"></a>Microsoft. szinapszis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Igen | Yes |
> | munkaterületek | Igen | Yes |
> | munkaterületek/bigDataPools | Igen | Yes |
> | munkaterületek/operationStatuses | Nem | Nem |
> | munkaterületek/sqlDatabases | Igen | Yes |
> | munkaterületek/sqlPools | Igen | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | környezetben | Igen | Nem |
> | környezetek/accessPolicies | Nem | Nem |
> | környezetek/eventsources | Igen | Nem |
> | környezetek/referenceDataSets | Igen | Nem |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | tárolja | Igen | Yes |
> | üzletek/accessPolicies | Nem | Nem |
> | üzletek/szolgáltatások | Nem | Nem |
> | üzletek/szolgáltatások/jogkivonatok | Nem | Nem |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Igen | Yes |
> | imageTemplates / runOutputs | Nem | Nem |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | ArcZones | Igen | Yes |
> | ResourcePools | Igen | Yes |
> | VCenter | Igen | Yes |
> | VirtualMachines | Igen | Yes |
> | VirtualMachineTemplates | Igen | Yes |
> | VirtualNetworks | Igen | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Igen | Yes |
> | dedicatedCloudServices | Igen | Yes |
> | virtualMachines | Igen | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | eszközök | Igen | Yes |
> | registeredSubscriptions | Nem | Nem |
> | szállítók | Nem | Nem |
> | szállítók/SKU-i | Nem | Nem |
> | szállítók/vnfs | Nem | Nem |
> | virtualNetworkFunctionSkus | Nem | Nem |
> | vnfs | Igen | Yes |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Nem |
> | tervek | Igen | Nem |
> | registeredSubscriptions | Nem | Nem |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nem | Nem |
> | apiManagementAccounts / apiAcls | Nem | Nem |
> | apiManagementAccounts/API-k | Nem | Nem |
> | apiManagementAccounts/API-k/apiAcls | Nem | Nem |
> | apiManagementAccounts/API-k/connectionAcls | Nem | Nem |
> | apiManagementAccounts/API-k/kapcsolatok | Nem | Nem |
> | apiManagementAccounts/API-k/kapcsolatok/connectionAcls | Nem | Nem |
> | apiManagementAccounts/API-k/localizedDefinitions | Nem | Nem |
> | apiManagementAccounts / connectionAcls | Nem | Nem |
> | apiManagementAccounts/kapcsolatok | Nem | Nem |
> | billingMeters | Nem | Nem |
> | tanúsítványok | Igen | Yes |
> | connectionGateways | Igen | Yes |
> | kapcsolatok | Igen | Yes |
> | customApis | Igen | Yes |
> | deletedSites | Nem | Nem |
> | hostingEnvironments | Igen | Yes |
> | hostingEnvironments / eventGridFilters | Nem | Nem |
> | hostingEnvironments / multiRolePools | Nem | Nem |
> | hostingEnvironments / workerPools | Nem | Nem |
> | kubeEnvironments | Igen | Yes |
> | publishingUsers | Nem | Nem |
> | javaslatok | Nem | Nem |
> | resourceHealthMetadata | Nem | Nem |
> | Runtimes | Nem | Nem |
> | Kiszolgálófarmok | Igen | Yes |
> | Kiszolgálófarmok/eventGridFilters | Nem | Nem |
> | Kiszolgálófarmok/firstPartyApps | Nem | Nem |
> | Kiszolgálófarmok/firstPartyApps/keyVaultSettings | Nem | Nem |
> | helyek | Igen | Yes |
> | helyek/konfiguráció  | Nem | Nem |
> | helyek/eventGridFilters | Nem | Nem |
> | helyek/hostNameBindings | Nem | Nem |
> | helyek/networkConfig | Nem | Nem |
> | helyek/premieraddons | Igen | Yes |
> | helyek/bővítőhelyek | Igen | Yes |
> | helyek/bővítőhelyek/eventGridFilters | Nem | Nem |
> | helyek/bővítőhelyek/hostNameBindings | Nem | Nem |
> | helyek/bővítőhelyek/networkConfig | Nem | Nem |
> | sourceControls | Nem | Nem |
> | staticSites | Igen | Yes |
> | érvényesít | Nem | Nem |
> | verifyHostingEnvironmentVnet | Nem | Nem |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nem | Nem |
> | diagnosticSettingsCategories | Nem | Nem |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Igen | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Igen | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | számítási feladatok | Igen | Yes |
> | munkaterhelések/példányok | Nem | Nem |
> | munkaterhelések/verziók | Nem | Nem |
> | munkaterhelések/verziók/összetevők | Nem | Nem |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | összetevők | Nem | Nem |
> | componentsSummary | Nem | Nem |
> | monitorInstances | Nem | Nem |
> | monitorInstancesSummary | Nem | Nem |
> | figyeli | Nem | Nem |
> | notificationSettings | Nem | Nem |

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan alkalmazhat címkéket az erőforrásokra, tekintse meg [a címkék használata az Azure-erőforrások rendszerezéséhez](tag-resources.md)című témakört.
