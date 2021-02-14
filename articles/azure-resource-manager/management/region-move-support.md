---
title: Azure-erőforrások régiók közötti áthelyezésének támogatása
description: Az Azure-régiók között áthelyezhető Azure-erőforrástípusok listája
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 18d4d84462d528b718d784ff6a16ecf990ed0d20
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094015"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Azure-erőforrások régiók közötti áthelyezésének támogatása

Ez a cikk megerősíti, hogy egy Azure-erőforrástípus támogatott-e egy másik Azure-régióba való áthelyezéshez. 

Ugrás erőforrás-szolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft. HRE](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft. igazolás](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. számlázás](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. Blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. felhasználás](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. Tárolószolgáltatás](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataConnect](#microsoftdataconnect)
> - [Microsoft. DataExchange](#microsoftdataexchange)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLake](#microsoftdatalake)
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
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. segédösszetevője](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. kísérletezés](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. features](#microsoftfeatures)
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
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kulcstartó](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
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
> - [Microsoft. hatáskörébe](#microsoftpurview)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. Quantum](#microsoftquantum)
> - [Microsoft. Recoveryservices szolgáltatónál](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. előfizetés](#microsoftsubscription)
> - [Microsoft. support](#microsoftsupport)
> - [Microsoft. szinapszis](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. HRE

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | domainservices | Nem | 


## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | diagnosticsettings | Nem |
> | diagnosticsettingscategories | Nem |
> | privatelinkforazuread | Nem |
> | bérlők |  Nem |

## <a name="microsoftaddons"></a>Microsoft. Addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | supportproviders | Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | aadsupportcases | Nem |
> | addsservices | Nem | 
> | ügynökök | Nem | 
> | anonymousapiusers | Nem |
> | konfiguráció | Nem | 
> | naplók | Nem | 
> | jelentések | Nem | 
> | servicehealthmetrics | Nem | 
> | services | Nem | 

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | konfigurációk | Nem | 
> | generaterecommendations | Nem |
> | metaadatok | Nem |
> | javaslatok | Nem |
> | fóliakondenzát | Nem | 

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | actionrules | Nem | 
> | riasztások | Nem | 
> | alertslist | Nem | 
> | alertsmetadata | Nem | 
> | alertssummary | Nem | 
> | alertssummarylist | Nem | 
> | smartdetectoralertrules | Nem | 
> | smartgroups | Nem | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kiszolgálók | Nem |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | reportfeedback | Nem |
> | szolgáltatás |  Igen (sablon használatával) <br/><br/> [API Management áthelyezése a régiók között](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | configurationstores | Nem | 
> | configurationstores / eventgridfilters | Nem |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | Spring | Nem | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | apiapps | Igen (sablon használatával)<br/><br/> [App Service alkalmazás áthelyezése másik régióba](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Nem | 
> | átjárók | Nem | 

## <a name="microsoftattestation"></a>Microsoft. igazolás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | attestationproviders | Nem | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | classicadministrators | Nem | 
> | dataaliases | Nem | 
> | denyassignments | Nem | 
> | elevateaccess | Nem | 
> | findorphanroleassignments | Nem | 
> | zárak | Nem | 
> | engedélyek | Nem | 
> | policyassignments | Nem | 
> | policydefinitions | Nem | 
> | policysetdefinitions | Nem | 
> | privatelinkassociations | Nem | 
> | resourcemanagementprivatelinks | Nem | 
> | RoleAssignments | Nem | 
> | roleassignmentsusagemetrics | Nem | 
> | roledefinitions | Nem | 

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | automationaccounts | Igen (sablon használatával) <br/><br/> [Geo-replikáció használata](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfigurációk | Nem | 
> | automationaccounts/runbookok | Nem | 

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | Előfizetés |
> | ------------- | ----------- | 
> | privateclouds | Nem | 


## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | b2cdirectories | Nem | 
> | b2ctenants | Nem | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datacontrollers | Nem | 
> | hybriddatamanagers | Nem | 
> | postgresinstances | Nem | 
> | sqlinstances | Nem | 
> | sqlmanagedinstances | Nem |
> | sqlserverinstances | Nem | 
> | sqlserverregistrations | Nem |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | cloudmanifestfiles | Nem |
> | regisztrációk | Nem | 

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | fürtök | Nem | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | batchaccounts |  A Batch-fiókok nem helyezhetők át közvetlenül az egyik régióból a másikba, de sablon használatával exportálhat egy sablont, módosíthatja azt, és üzembe helyezheti a sablont az új régióban. <br/><br/> Tudnivalók [a Batch-fiókok régiók közötti áthelyezéséről](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft. számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | billingaccounts | Nem | 
> | billingperiods | Nem | 
> | billingpermissions | Nem | 
> | billingproperty | Nem | 
> | billingroleassignments | Nem | 
> | billingroledefinitions | Nem | 
> | részlegek | Nem | 
> | enrollmentaccounts | Nem | 
> | számlák | Nem | 
> | transzferek | Nem | 

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | mapapis | Nem | 

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | biztalk | Nem | 

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | blockchainmembers | Nem <br/><br/> A blockchain-hálózat nem rendelkezhet különböző régiókban található csomópontokkal. 
> | cordamembers | Nem |
> | Watchers | Nem | 

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tokenservices | Nem |


## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | blueprintassignments | Nem | 
> | tervrajzok | Nem |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | botservices | Nem | 

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Redis | Nem | 
> | redisenterprise | Nem | 

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | appliedreservations | Nem | 
> | calculateexchange | Nem | 
> | calculateprice | Nem | 
> | calculatepurchaseprice | Nem | 
> | katalógusok | Nem | 
> | commercialreservationorders | Nem | 
> | Exchange | Nem |
> | reservationorders | Nem | 
> | foglalások | Nem | 
> | resources | Nem | 
> | validatereservationorder | Nem | 

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nem |
> | edgenodes | Nem
> | profilok | Nem | 
> | profilok/végpontok | Nem | 

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tanúsítványrendelések | Nem | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | képességek | Nem | 
> | tartománynevek | Igen | Nem |
> | kvóták | Nem | 
> | resourcetypes | Nem |
> | validatesubscriptionmoveavailability | Nem | 
> | virtualmachines | Nem 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | Nem | 

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | képességek | Nem | 
> | expressroutecrossconnections | Nem | 
> | expressroutecrossconnections/társak | Nem | 
> | gatewaysupporteddevices | Nem | 
> | networksecuritygroups | Nem |
> | kvóták | Nem |
> | reservedips | Nem | 
> | virtualnetworks | Nem | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | lemezek | Nem | 
> | images | Nem | 
> | osimages | Nem | 
> | osplatformimages | Nem | 
> | publicimages | Nem | 
> | kvóták | Nem | 
> | storageaccounts | Igen |  
> | lemezképet | Nem |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | műveletek | Nem | 

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 
> | Cognitive Search | Manuális lépések esetén támogatott.<br/><br/> Ismerje meg [, hogyan helyezheti át Azure Cognitive Search szolgáltatását egy másik régióba](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | ratecard | Nem | 
> | usageaggregates | Nem | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | availabilitysets | Igen <br/><br/> A rendelkezésre állási csoportok áthelyezéséhez használja az [Azure erőforrás-mozgatót](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | diskaccesses | Nem |
> | diskencryptionsets | Nem | 
> | lemezek | Igen <br/><br/> Az Azure-beli virtuális gépek és a kapcsolódó lemezek áthelyezéséhez használja az [Azure Resource mozgatót](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | katalógusok | Nem | 
> | galériák/lemezképek | Nem | 
> | galériák/lemezképek/verziók | Nem | 
> | hostgroups | Nem | 
> | hostgroups/gazdagépek | Nem | 
> | images | Nem | 
> | proximityplacementgroups | Nem | 
> | restorepointcollections | Nem | 
> | sharedvmimages | Nem | 
> | sharedvmimages/verziók | Nem | 
> | pillanatképek | Nem | 
> | sshpublickeys | Nem |
> | virtualmachines | Igen <br/><br/> Azure-beli virtuális gépek áthelyezése az Azure-beli [erőforrás-mozgató](../../resource-mover/tutorial-move-region-virtual-machines.md) használatával. | 
> | virtualmachines/bővítmények | Nem | 
> | virtualmachinescalesets | Nem | 

## <a name="microsoftconsumption"></a>Microsoft. felhasználás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | aggregatedcost | Nem | 
> | elosztja | Nem | 
> | költségvetése | Nem | 
> | díjak | Nem | 
> | costtags | Nem | 
> | hitelek | Nem | 
> | események | Nem | 
> | előrejelzések | Nem | 
> | számos | Nem | 
> | piacterek | Nem | 
> | pricesheets | Nem | 
> | termékek | Nem | 
> | reservationdetails | Nem | 
> | reservationrecommendationdetails | Nem | 
> | reservationrecommendations | Nem | 
> | reservationsummaries | Nem | 
> | reservationtransactions | Nem | 
> | tags | Nem | 
> | bérlők | Nem | 
> | feltételek | Nem | 
> | UsageDetails | Nem | 


## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | containergroups | Nem | 
> | serviceassociationlinks | Nem |


## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kibocsátásiegység | Nem |  
> | kibocsátásiegység-forgalmi jegyzékek/agentpools | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks | Nem |  
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | Nem |  
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | Nem | 

## <a name="microsoftcontainerservice"></a>Microsoft. Tárolószolgáltatás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | containerservices | Nem |
> | managedclusters | Nem | 
> | openshiftmanagedclusters | Nem | 

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások | Nem | 

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | riasztások | Nem | 
> | billingaccounts | Nem | 
> | költségvetése | Nem | 
> | cloudconnectors | Nem | 
> | összekötők | Nem | 
> | részlegek | Nem | 
> | Méretek | Nem | 
> | enrollmentaccounts | Nem | 
> | export | Nem | 
> | externalbillingaccounts | Nem | 
> | forecast | Nem | 
> | lekérdezés | Nem | 
> | Regisztráció | Nem | 
> | reportconfigs | Nem | 
> | jelentések | Nem | 
> | beállítások | Nem | 
> | showbackrules | Nem | 
> | kilátással | Nem | 

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Hubs | Nem |  

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | kérelmek | Nem | 

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | szövetségek | Nem |
> | resourceproviders | Nem | 

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | feladatok | Nem | 

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | availableskus | Nem |
> | databoxedgedevices | Nem | 

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületek | Nem | 

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | katalógusok | Nem | 
> | datacatalogs | Nem | 

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | connectionmanagers | Nem | 

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | csomagok | Nem | 
> | tervek | Nem | 

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datafactories | Nem | 
> | előállítók | Nem |  

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datalakeaccounts | Nem | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftdatalakestore"></a>Microsoft. Data Lake Store

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | Nem | 
> | szolgáltatások/projektek | Nem | 
> | bővítőhely | Nem | 

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | ---------- |
> | backupvaults | Nem | 

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kiszolgálók | Egy meglévő kiszolgáló áthelyezéséhez a régiók közötti olvasási replikát használhat. [További információ](../../postgresql/howto-move-regions-portal.md).<br/><br/> Ha a szolgáltatás a Geo-redundáns biztonsági mentési tárolóval van kiépítve, a Geo-visszaállítás használatával más régiókban is visszaállítható. [További információ](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kiszolgálók | Egy meglévő kiszolgáló áthelyezéséhez a régiók közötti olvasási replikát használhat. [További információ](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | servergroups | Nem | 
> | kiszolgálók | Egy meglévő kiszolgáló áthelyezéséhez a régiók közötti olvasási replikát használhat. [További információ](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Nem | 

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | artifactsources | Nem | 
> | kibocsátások | Nem |  
> | servicetopologies | Nem | 
> | servicetopologies/szolgáltatások | Nem |  
> | servicetopologies/szolgáltatások/serviceunits | Nem | 
> | lépések | Nem | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | applicationgroups | Nem | 
> | munkaterületek | Nem | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | elasticpools | Nem. Az erőforrás nincs kitéve.
> | elasticpools / iothubtenants | Nem. Az erőforrás nincs kitéve.
> | iothubs | Igen. [További információ](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Nem | 

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományvezérlők | Nem | 


## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományvezérlők | Nem | 
> | AK-fürt | Nem<br/><br/> [További](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) információ a másik régióba való áttérésről.

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | labcenters | Nem | 
> | Labs | Nem | 
> | Labs/környezetek | Nem |  
> | Labor/servicerunners | Nem | 
> | Labor/virtualmachines | Nem |  
> | menetrend | Nem |  

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Igen, az erőforrások új régióban való újbóli létrehozásával. [További információ](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | databaseaccounts | Nem | 
> | databaseaccounts | Nem | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományok | Nem | 
> | generatessorequest | Nem | 
> | topleveldomains | Nem | 
> | validatedomainregistrationinformation | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | Nem |  

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományok | Nem | 
> | eventsubscriptions | Nem |
> | extensiontopics | Nem | 
> | partnernamespaces | Nem | 
> | partnerregistrations | Nem | 
> | partnertopics | Nem | 
> | systemtopics | Nem | 
> | témakörök | Nem | 
> | topictypes | Nem | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | Nem |  
> | névterek | Igen (sablonnal)<br/><br/> [Event hub-névtér áthelyezése másik régióba](../../event-hubs/move-across-regions.md) | 
> | SKU | Nem |  

## <a name="microsoftexperimentation"></a>Microsoft. kísérletezés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | experimentworkspaces | Nem | 

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | névterek | Nem | 

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | featureproviders | Nem | 
> | funkciók | Nem | 
> | szolgáltatók | Nem | 
> | subscriptionfeatureregistrations | Nem | 

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | automanagedaccounts | Nem | 
> | automanagedvmconfigurationprofiles | Nem | 
> | guestconfigurationassignments | Nem | 
> | szoftver | Nem | 
> | softwareupdateprofile | Nem | 
> | softwareupdates | Nem | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | hanainstances | Nem | 
> | sapmonitors | Nem |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | dedicatedhsms | Nem | 


## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | Nem | 

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | Nem |  

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | gépek | Nem | 
> | gépek/bővítmények | Nem |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datamanagers |  Nem | 

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | eszközök | Nem | 
> | vnfs | Nem | 

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | összetevők | Nem | 
> | networkscopes | Nem | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | feladatok |  Nem | 

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem. [További információ](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | actiongroups |  Nem | 
> | activitylogalerts | Nem | 
> | alertrules |  Nem | 
> | autoscalesettings |  Nem | 
> | alapkonfiguráció | Nem |
> | összetevők |  Nem |  
> | datacollectionrules | Nem | 
> | diagnosticsettings | Nem | 
> | diagnosticsettingscategories | Nem | 
> | eventcategories | Nem | 
> | eventtypes | Nem | 
> | extendeddiagnosticsettings | Nem | |
> | guestdiagnosticsettings | Nem | 
> | listmigrationdate | Nem | 
> | logdefinitions | Nem | 
> | logprofiles | Nem | 
> | naplók | Nem | Nem |
> | metricalerts | Nem | 
> | metricbaselines | Nem | 
> | metricbatch | Nem | 
> | metricdefinitions | Nem | 
> | metricnamespaces | Nem | 
> | metrics | Nem | 
> | migratealertrules | Nem |
> | migratetonewpricingmodel | Nem | 
> | myworkbooks | Nem |
> | notificationgroups | Nem | 
> | privatelinkscopes | Nem |
> | rollbacktolegacypricingmodel | Nem |
> | scheduledqueryrules |  Nem | 
> | topology | Nem |
> | transactions | Nem |
> | vminsightsonboardingstatuses | Nem |
> | webteszteket |  Nem | 
> | webtesztek/gettestresultfile | Nem |
> | munkafüzetek |  Nem |  
> | workbooktemplates | Nem |


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | apptemplates | Nem | 
> | iotapps | Nem | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> |  iothub |  Igen (klónozási központ) <br/><br/> [IoT-központ klónozása egy másik régióba](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése |
> | ------------- | ----------- | 
> | Graph | Nem | 

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | deletedvaults | Nem |
> | hsmpools | Nem | 
> | managedhsms | Nem |
> | boltívek |  Nem | 

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | connectedclusters | Nem | 
> | registeredsubscriptions | Nem | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | Nem | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök |  Nem |  

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | labaccounts | Nem | 
> | felhasználók | Nem | 

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem, ez egy globális szolgáltatás.

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | hostingenvironments | Nem | 
> | integrationaccounts |  Nem |  
> | integrationserviceenvironments | Nem | 
> | integrationserviceenvironments/król | Nem |
> | isolatedenvironments | Nem | 
> | munkafolyamatok |  Nem |  

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | commitmentplans |  Nem | 
> | WebServices |  Nem | 
> | munkaterületek |  Nem | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | operationalizationclusters |  Nem | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 
> | teamaccounts | Nem | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületek | Nem | 

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | configurationassignments | Igen. [További információ](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Igen. [További információ](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | frissítések | Nem | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | identitások | Nem | 
> | userassignedidentities | Nem | 

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | managednetworks | Nem | 
> | managednetworks / managednetworkgroups | Nem |
> | managednetworks / managednetworkpeeringpolicies | Nem | 
> | értesítés | Nem | 

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | Nem | 
> | registrationassignments | Nem |
> | registrationdefinitions | Nem | 

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | getentities | Nem | 
> | managementgroups | Nem | 
> | managementgroups/beállítások | Nem | 
> | resources | Nem | 
> | starttenantbackfill | Nem | 
> | tenantbackfillstatus | Nem | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok |  Nem, Azure Maps térinformatikai szolgáltatás. 
> | fiókok/privateatlases | Nem

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kínál | Nem | 
> | offertypes | Nem | 
> | privategalleryitems | Nem | 
> | privatestoreclient | Nem | 
> | privatestores | Nem | 
> | termékek | Nem | 
> | közzétevők | Nem | 
> | Regisztráció | Nem | 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | classicdevservices | Nem | 

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | megállapodások | Nem | 
> | offertypes | Nem | 

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Mediaservices |  Nem | 
> | Mediaservices/liveevents |  Nem | 
> | Mediaservices/streamingendpoints |  Nem | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | appclusters | Nem | 

## <a name="microsoftmigrate"></a>Microsoft. Migrálás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | assessmentprojects | Nem | 
> | migrateprojects | Nem | 
> | movecollections | Nem
> | projektek | Nem | 

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nem | 
> | objectunderstandingaccounts | Nem | 
> | remoterenderingaccounts | Nem | 
> | spatialanchorsaccounts | Nem | 

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | netappaccounts | Nem | 
> | netappaccounts / capacitypools | Nem | 
> | netappaccounts/capacitypools/kötetek | Nem | 
> | netappaccounts/capacitypools/kötetek/mounttargets | Nem | 
> | netappaccounts/capacitypools/kötetek/Pillanatképek | Nem | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | applicationgateways | Nem |
> | applicationgatewaywebapplicationfirewallpolicies | Nem | 
> | applicationsecuritygroups |  Nem |  
> | azurefirewalls |  Nem |  
> | bastionhosts | Nem | 
> | bgpservicecommunities | Nem |
> | kapcsolatok |  Nem | 
> | ddoscustompolicies |  Nem | 
> | ddosprotectionplans | Nem | 
> | dnszones |  Nem | 
> | expressroutecircuits | Nem | 
> | expressroutegateways | Nem | 
> | expressrouteserviceproviders | Nem | 
> | firewallpolicies | Nem |
> | frontdoors | Nem | 
> | ipallocations | Nem |
> | ipgroups | Nem |
> | loadbalancers | Igen <br/><br/> Az [Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) -beli erőforrás-mozgató használatával belső és külső terheléselosztó helyezhető át. |
> | localnetworkgateways |  Nem | 
> | natgateways |  Nem | 
> | networkexperimentprofiles | Nem |
> | networkintentpolicies |  Nem | 
> | networkinterfaces | Igen <br/><br/> A hálózati adapterek áthelyezéséhez használja az [Azure erőforrás-mozgatót](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkprofiles | Nem | 
> | networksecuritygroups | Igen <br/><br/> A hálózati biztonsági csoportok (NGSs) áthelyezéséhez használja az [Azure erőforrás-mozgatót](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkwatchers |  Nem |  
> | networkwatchers / connectionmonitors |  Nem | 
> | networkwatchers / flowlogs |  Nem | 
> | networkwatchers / pingmeshes |  Nem | 
> | p2svpngateways | Nem | 
> | privatednszones |  Nem |  
> | privatednszones / virtualnetworklinks | Nem |> | privatednszonesinternal | Nem |
> | privateendpointredirectmaps | Nem |
> | privateendpoints | Nem | 
> | privatelinkservices | Nem | 
> | nyilvános IP | Igen<br/><br/> A nyilvános IP-címek áthelyezéséhez használja az [Azure erőforrás-mozgatót](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | publicipprefixes | Nem | 
> | routefilters | Nem | 
> | routetables |  Nem | 
> | securitypartnerproviders | Nem |
> | serviceendpointpolicies |  Nem | 
> | trafficmanagergeographichierarchies | Nem | 
> | trafficmanagerprofiles |  Nem | 
> | trafficmanagerusermetricskeys | Nem |
> | virtualhubs | Nem | 
> | virtualnetworkgateways |  Nem |  
> | virtualnetworks |  Nem | 
> | virtualnetworktaps | Nem | 
> | virtualwans | Nem | 
> | vpngateways (virtuális WAN) | Nem | 
> | vpnsites (virtuális WAN) | Nem | 
> | vpnsites (virtuális WAN) | Nem |


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | névterek |  Nem | 
> | névterek/notificationhubs |  Nem |  

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | osnamespaces | Nem | 

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | hypervsites | Nem | 
> | importsites | Nem | 
> | serversites | Nem | 
> | vmwaresites | Nem | 

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | Nem | 
> | deletedworkspaces | Nem | 
> | linktargets | Nem | 
> | storageinsightconfigs | Nem |
> | munkaterületek | Nem |



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | managementassociations | Nem |
> | managementconfigurations |  Nem | 
> | megoldások | Nem |
> | kilátással |  Nem | 

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | legacypeerings | Nem | 
> | peerasns | Nem | 
> | peeringlocations | Nem | 
> | társviszonyok | Nem | 
> | peeringservicecountries | Nem | 
> | peeringservicelocations | Nem | 
> | peeringserviceproviders | Nem | 
> | peeringservices | Nem | 

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | policyevents | Nem | 
> | policystates | Nem | 
> | policytrackedresources | Nem | 
> | szervizelések | Nem | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> |  -konzolok | Nem |
> | irányítópultok | Nem | 
> | usersettings | Nem | 


## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | workspacecollections |  Nem | 

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kapacitások |  Nem | 

## <a name="microsoftpurview"></a>Microsoft. hatáskörébe

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | availableaccounts | Nem | 
> | providerregistrations | Nem | 
> | kibocsátások | Nem | 

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | munkaterületek | Nem | 

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | replicationeligibilityresults | Nem |
> | boltívek | Nem.<br/><br/> Az Azure-régiók Azure Backup Recovery Services-tárolóinak áthelyezése nem támogatott.<br/><br/> Azure Site Recovery Recovery Services-tárolójában [letilthatja és újból létrehozhatja](../../site-recovery/move-vaults-across-regions.md) a tárolót a célhelyen. | 

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | openshiftclusters | Nem | 

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | névterek |  Nem | 

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | lekérdezések |  Nem |  
> | resourcechangedetails | Nem | 
> | resourcechanges | Nem | 
> | resources | Nem | 
> | resourceshistory | Nem | 
> | subscriptionsstatus | Nem | 

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | childresources | Nem | 
> | emergingissues | Nem | 
> | események | Nem | 
> | metaadatok | Nem | 
> | értesítések | Nem | 

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése |
> | ------------- | ----------- |
> | deploymentScripts |  Igen<br/><br/>[A Microsoft. Resources erőforrásainak áthelyezése új régióba](microsoft-resources-move-regions.md) |
> | templateSpecs |  Igen<br/><br/>[A Microsoft. Resources erőforrásainak áthelyezése új régióba](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások |  Nem | 
> | saasresources | Nem | 


## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | resourcehealthmetadata | Nem |
> | searchservices |  Nem | 


## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | Nem | 
> | advancedthreatprotectionsettings | Nem | 
> | riasztások | Nem | 
> | allowedconnections | Nem | 
> | applicationwhitelistings | Nem | 
> | assessmentmetadata | Nem | 
> | értékelések | Nem | 
> | autodismissalertsrules | Nem | 
> | automatizálások szabványának létrehozásában | Nem | 
> | autoprovisioningsettings | Nem |
> | complianceresults | Nem | 
> | felelésről | Nem | 
> | datacollectionagents | Nem | 
> | devicesecuritygroups | Nem | 
> | discoveredsecuritysolutions | Nem | 
> | externalsecuritysolutions | Nem | 
> | informationprotectionpolicies | Nem | 
> | iotsecuritysolutions | Nem | 
> | iotsecuritysolutions / analyticsmodels | Nem | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nem | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nem | 
> | jitnetworkaccesspolicies | Nem | 
> | policies | Nem | 
> | pricings | Nem | 
> | regulatorycompliancestandards | Nem | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | Nem | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nem | 
> | securitycontacts | Nem | 
> | securitysolutions | Nem | 
> | securitysolutionsreferencedata | Nem | 
> | securitystatuses | Nem | 
> | securitystatusessummaries | Nem | 
> | servervulnerabilityassessments | Nem | 
> | beállítások | Nem | 
> | alértékelések | Nem |
> | feladatok | Nem | 
> | topológiák | Nem | 
> | workspacesettings | Nem | 

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | összesítések | Nem | 
> | alertrules | Nem | 
> | alertruletemplates | Nem | 
> | automationrules | Nem |
> | esetekben | Nem | 
> | dataconnectors | Nem | 
> | szervezetek | Nem | 
> | entityqueries | Nem |
> | incidensek | Nem | 
> | officeconsents | Nem | 
> | beállítások | Nem | 
> | threatintelligence | Nem | 

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | consoleservices | Nem | 

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | átjárók | Nem | 
> | csomópontok | Nem | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | névterek |  Nem | 
> | premiummessagingregions | Nem | 
> | SKU | Nem | 

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások | Nem | 
> | fürtök |  Nem |  
> | containergroups | Nem | 
> | containergroupsets | Nem | 
> | edgeclusters | Nem | 
> | managedclusters | Nem |
> | hálózatok | Nem | 
> | secretstores | Nem | 
> | volumes | Nem | 

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások |  Nem | 
> | containergroups | Nem | 
> | átjárók |  Nem | 
> | hálózatok |  Nem | 
> | titkok |  Nem | 
> | volumes |  Nem |  

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | kibocsátások | Nem | 

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | signalr |  Nem |  

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | hybridusebenefits | Nem | 

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | appliancedefinitions | Nem | 
> | berendezések | Nem | 
> | jitrequests | Nem | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | instancepools | Nem | 
> | helyek | Nem |
> | managedinstances | Igen <br/><br/> [További](../../azure-sql/database/move-resources-across-regions.md) információ a felügyelt példányok régiók közötti áthelyezéséről. | 
> | managedinstances/adatbázisok | Igen | 
> | kiszolgálók | Igen | 
> | kiszolgálók/adatbázisok | Igen <br/><br/> [További](../../azure-sql/database/move-resources-across-regions.md) információ az adatbázisok régiók közötti áthelyezéséről.<br/><br/> [További](../../resource-mover/tutorial-move-region-sql.md) információ az Azure-beli erőforrás-mozgató Azure SQL Database-adatbázisok áthelyezéséhez való használatáról.  | 
> | kiszolgálók/elasticpools | Igen <br/><br/> [További](../../azure-sql/database/move-resources-across-regions.md) információ a rugalmas készletek régiók közötti áthelyezéséről.<br/><br/> [További](../../resource-mover/tutorial-move-region-sql.md) információ az Azure-beli erőforrás-mozgató Azure SQL rugalmas készletek áthelyezéséhez való használatáról.  | 
> | virtualclusters | Igen | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Nem |  
> | sqlvirtualmachines |  Nem |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storageaccounts | Igen<br/><br/> [Azure Storage-fiók áthelyezése másik régióba](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | gyorsítótárak | Nem | 

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices |  Nem | 

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices | Nem | 

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices | Nem | 

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kezelők | Nem | 

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | Nem |
> | streamingjobs |  Nem |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | környezetben | Nem | 
> | esetben | Nem | 

## <a name="microsoftsubscription"></a>Microsoft. előfizetés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | előfizetések | Nem | 

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | services | Nem | 
> | supporttickets | Nem | 

## <a name="microsoftsynapse"></a>Microsoft. szinapszis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | munkaterületek | Nem | 
> | munkaterületek/bigdatapools | Nem | 
> | munkaterületek/sqlpools | Nem | 


## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | környezetben |  Nem | 
> | környezetek/eventsources |  Nem |  
> | környezetek/referencedatasets |  Nem | 

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tárolja | Nem | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | imagetemplates | Nem | 

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | account |  Nem | 
> | fiók/bővítmény |  Nem | 
> | fiók/projekt |  Nem | 

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | arczones | Nem | 
> | resourcepools | Nem | 
> | vCenter | Nem | 
> | virtualmachines | Nem | 
> | virtualmachinetemplates | Nem | 
> | virtualnetworks | Nem | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Nem | 
> | dedicatedcloudservices | Nem | 
> | virtualmachines | Nem | 

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | eszközök | Nem | 
> | vnfs | Nem | 

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | fiókok | Nem | 
> | tervek | Nem | 
> | registeredsubscriptions | Nem |


## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | availablestacks | Nem | 
> | billingmeters | Nem | 
> | tanúsítványok | Nem | 
> | connectiongateways |  Nem |  
> | kapcsolatok |  Nem |  
> | customapis |  Nem | 
> | deletedsites | Nem | 
> | deploymentlocations | Nem | 
> | georegions | Nem | 
> | hostingenvironments | Nem | 
> | kubeenvironments | Nem | 
> | publishingusers | Nem |
> | javaslatok | Nem | 
> | resourcehealthmetadata | Nem | 
> | Runtimes | Nem | 
> | kiszolgálófarmok | Nem |  
> | kiszolgálófarmok/eventgridfilters | N
> | helyek |  Nem | 
> | helyek/premieraddons |  Nem |  
> | helyek/bővítőhelyek |  Nem |  
> | sourcecontrols | Nem |
> | staticsites | Nem | 

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | multipleactivationkeys | Nem |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | deviceservices | Nem | 

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | számítási feladatok | Nem | 

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | összetevők | Nem |
> | componentssummary | Nem | 
> | monitorinstances | Nem | 
> | monitorinstancessummary | Nem | 
> | figyeli | Nem | 
## <a name="third-party-services"></a>Harmadik féltől származó szolgáltatások

A harmadik féltől származó szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.

## <a name="next-steps"></a>Következő lépések

[További](../../resource-mover/overview.md) információ az erőforrás-mozgató szolgáltatásról.

