---
title: Azure-erőforrások régiók közötti áthelyezésének támogatása
description: Az Azure-régiók között áthelyezhető Azure-erőforrástípusok listája
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 9229fca9f98aac4ca628c0bb25c13c9ba1989626
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962594"
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
> | domainservices | No | 


## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | diagnosticsettings | No |
> | diagnosticsettingscategories | No |
> | privatelinkforazuread | No |
> | bérlők |  No |

## <a name="microsoftaddons"></a>Microsoft. Addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | supportproviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | aadsupportcases | No |
> | addsservices | No | 
> | ügynökök | No | 
> | anonymousapiusers | No |
> | konfiguráció | No | 
> | naplók | No | 
> | jelentések | No | 
> | servicehealthmetrics | No | 
> | services | No | 

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | konfigurációk | No | 
> | generaterecommendations | No |
> | metaadatok | No |
> | javaslatok | No |
> | fóliakondenzát | No | 

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | actionrules | No | 
> | riasztások | No | 
> | alertslist | No | 
> | alertsmetadata | No | 
> | alertssummary | No | 
> | alertssummarylist | No | 
> | smartdetectoralertrules | No | 
> | smartgroups | No | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kiszolgálók | No |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | reportfeedback | No |
> | szolgáltatás |  Igen (sablon használatával) <br/><br/> [API Management áthelyezése a régiók között](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | configurationstores | No | 
> | configurationstores / eventgridfilters | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | Spring | No | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | apiapps | Igen (sablon használatával)<br/><br/> [App Service alkalmazás áthelyezése másik régióba](../../app-service/manage-move-across-regions.md) | 
> | appidentities | No | 
> | átjárók | No | 

## <a name="microsoftattestation"></a>Microsoft. igazolás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | attestationproviders | No | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | classicadministrators | No | 
> | dataaliases | No | 
> | denyassignments | No | 
> | elevateaccess | No | 
> | findorphanroleassignments | No | 
> | zárak | No | 
> | engedélyek | No | 
> | policyassignments | No | 
> | policydefinitions | No | 
> | policysetdefinitions | No | 
> | privatelinkassociations | No | 
> | resourcemanagementprivatelinks | No | 
> | RoleAssignments | No | 
> | roleassignmentsusagemetrics | No | 
> | roledefinitions | No | 

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | automationaccounts | Igen (sablon használatával) <br/><br/> [Geo-replikáció használata](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfigurációk | No | 
> | automationaccounts/runbookok | No | 

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | Előfizetés |
> | ------------- | ----------- | 
> | privateclouds | No | 


## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | b2cdirectories | No | 
> | b2ctenants | No | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datacontrollers | No | 
> | hybriddatamanagers | No | 
> | postgresinstances | No | 
> | sqlinstances | No | 
> | sqlmanagedinstances | No |
> | sqlserverinstances | No | 
> | sqlserverregistrations | No |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | cloudmanifestfiles | No |
> | regisztrációk | No | 

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | fürtök | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | batchaccounts |  A Batch-fiókok nem helyezhetők át közvetlenül az egyik régióból a másikba, de sablon használatával exportálhat egy sablont, módosíthatja azt, és üzembe helyezheti a sablont az új régióban. <br/><br/> Tudnivalók [a Batch-fiókok régiók közötti áthelyezéséről](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft. számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | billingaccounts | No | 
> | billingperiods | No | 
> | billingpermissions | No | 
> | billingproperty | No | 
> | billingroleassignments | No | 
> | billingroledefinitions | No | 
> | részlegek | No | 
> | enrollmentaccounts | No | 
> | számlák | No | 
> | transzferek | No | 

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | mapapis | No | 

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | biztalk | No | 

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | blockchainmembers | No <br/><br/> A blockchain-hálózat nem rendelkezhet különböző régiókban található csomópontokkal. 
> | cordamembers | No |
> | Watchers | No | 

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tokenservices | No |


## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | blueprintassignments | No | 
> | tervrajzok | No |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | botservices | No | 

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Redis | No | 
> | redisenterprise | No | 

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | appliedreservations | No | 
> | calculateexchange | No | 
> | calculateprice | No | 
> | calculatepurchaseprice | No | 
> | katalógusok | No | 
> | commercialreservationorders | No | 
> | Exchange | No |
> | reservationorders | No | 
> | foglalások | No | 
> | resources | No | 
> | validatereservationorder | No | 

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | No |
> | edgenodes | No
> | profilok | No | 
> | profilok/végpontok | No | 

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tanúsítványrendelések | No | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | képességek | No | 
> | tartománynevek | No |
> | kvóták | No | 
> | resourcetypes | No |
> | validatesubscriptionmoveavailability | No | 
> | virtualmachines | No 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | No | 

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | képességek | No | 
> | expressroutecrossconnections | No | 
> | expressroutecrossconnections/társak | No | 
> | gatewaysupporteddevices | No | 
> | networksecuritygroups | No |
> | kvóták | No |
> | reservedips | No | 
> | virtualnetworks | No | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | lemezek | No | 
> | images | No | 
> | osimages | No | 
> | osplatformimages | No | 
> | publicimages | No | 
> | kvóták | No | 
> | storageaccounts | Yes |  
> | lemezképet | No |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | műveletek | No | 

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | No | 
> | Cognitive Search | Manuális lépések esetén támogatott.<br/><br/> Ismerje meg [, hogyan helyezheti át Azure Cognitive Search szolgáltatását egy másik régióba](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | ratecard | No | 
> | usageaggregates | No | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | availabilitysets | Yes <br/><br/> A rendelkezésre állási csoportok áthelyezéséhez használja az [Azure erőforrás-mozgatót](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | diskaccesses | No |
> | diskencryptionsets | No | 
> | lemezek | Yes <br/><br/> Az Azure-beli virtuális gépek és a kapcsolódó lemezek áthelyezéséhez használja az [Azure Resource mozgatót](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | katalógusok | No | 
> | galériák/lemezképek | No | 
> | galériák/lemezképek/verziók | No | 
> | hostgroups | No | 
> | hostgroups/gazdagépek | No | 
> | images | No | 
> | proximityplacementgroups | No | 
> | restorepointcollections | No | 
> | sharedvmimages | No | 
> | sharedvmimages/verziók | No | 
> | pillanatképek | No | 
> | sshpublickeys | No |
> | virtualmachines | Yes <br/><br/> Azure-beli virtuális gépek áthelyezése az Azure-beli [erőforrás-mozgató](../../resource-mover/tutorial-move-region-virtual-machines.md) használatával. | 
> | virtualmachines/bővítmények | No | 
> | virtualmachinescalesets | No | 

## <a name="microsoftconsumption"></a>Microsoft. felhasználás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | aggregatedcost | No | 
> | elosztja | No | 
> | költségvetése | No | 
> | díjak | No | 
> | costtags | No | 
> | hitelek | No | 
> | események | No | 
> | előrejelzések | No | 
> | számos | No | 
> | piacterek | No | 
> | pricesheets | No | 
> | termékek | No | 
> | reservationdetails | No | 
> | reservationrecommendationdetails | No | 
> | reservationrecommendations | No | 
> | reservationsummaries | No | 
> | reservationtransactions | No | 
> | tags | No | 
> | bérlők | No | 
> | feltételek | No | 
> | UsageDetails | No | 


## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | containergroups | No | 
> | serviceassociationlinks | No |


## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kibocsátásiegység | No |  
> | kibocsátásiegység-forgalmi jegyzékek/agentpools | No | 
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks | No |  
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | No | 
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | No |  
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | No | 

## <a name="microsoftcontainerservice"></a>Microsoft. Tárolószolgáltatás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | containerservices | No |
> | managedclusters | No | 
> | openshiftmanagedclusters | No | 

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások | No | 

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | No | 

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | riasztások | No | 
> | billingaccounts | No | 
> | költségvetése | No | 
> | cloudconnectors | No | 
> | összekötők | No | 
> | részlegek | No | 
> | Méretek | No | 
> | enrollmentaccounts | No | 
> | export | No | 
> | externalbillingaccounts | No | 
> | forecast | No | 
> | lekérdezés | No | 
> | Regisztráció | No | 
> | reportconfigs | No | 
> | jelentések | No | 
> | beállítások | No | 
> | showbackrules | No | 
> | kilátással | No | 

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Hubs | No |  

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | kérelmek | No | 

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | szövetségek | No |
> | resourceproviders | No | 

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | feladatok | No | 

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | availableskus | No |
> | databoxedgedevices | No | 

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületek | No | 

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | katalógusok | No | 
> | datacatalogs | No | 

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | connectionmanagers | No | 

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | csomagok | No | 
> | tervek | No | 

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datafactories | No | 
> | előállítók | No |  

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datalakeaccounts | No | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | No | 

## <a name="microsoftdatalakestore"></a>Microsoft. Data Lake Store

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | No | 

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | No | 
> | szolgáltatások/projektek | No | 
> | bővítőhely | No | 

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | ---------- |
> | backupvaults | No | 

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | No | 

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kiszolgálók | Egy meglévő kiszolgáló áthelyezéséhez a régiók közötti olvasási replikát használhat. [További információk](../../postgresql/howto-move-regions-portal.md).<br/><br/> Ha a szolgáltatás a Geo-redundáns biztonsági mentési tárolóval van kiépítve, a Geo-visszaállítás használatával más régiókban is visszaállítható. [További információk](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kiszolgálók | Egy meglévő kiszolgáló áthelyezéséhez a régiók közötti olvasási replikát használhat. [További információk](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | servergroups | No | 
> | kiszolgálók | Egy meglévő kiszolgáló áthelyezéséhez a régiók közötti olvasási replikát használhat. [További információ](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | No | 

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | artifactsources | No | 
> | kibocsátások | No |  
> | servicetopologies | No | 
> | servicetopologies/szolgáltatások | No |  
> | servicetopologies/szolgáltatások/serviceunits | No | 
> | lépések | No | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | applicationgroups | No | 
> | munkaterületek | No | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | elasticpools | Nem. Az erőforrás nincs kitéve.
> | elasticpools / iothubtenants | Nem. Az erőforrás nincs kitéve.
> | iothubs | Igen. [További információ](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | No | 

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományvezérlők | No | 


## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományvezérlők | No | 
> | AK-fürt | No<br/><br/> [További](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) információ a másik régióba való áttérésről.

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | labcenters | No | 
> | Labs | No | 
> | Labs/környezetek | No |  
> | Labor/servicerunners | No | 
> | Labor/virtualmachines | No |  
> | menetrend | No |  

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Igen, az erőforrások új régióban való újbóli létrehozásával. [További információ](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | databaseaccounts | No | 
> | databaseaccounts | No | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományok | No | 
> | generatessorequest | No | 
> | topleveldomains | No | 
> | validatedomainregistrationinformation | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományok | No | 
> | eventsubscriptions | No |
> | extensiontopics | No | 
> | partnernamespaces | No | 
> | partnerregistrations | No | 
> | partnertopics | No | 
> | systemtopics | No | 
> | témakörök | No | 
> | topictypes | No | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | No |  
> | névterek | Igen (sablonnal)<br/><br/> [Event hub-névtér áthelyezése másik régióba](../../event-hubs/move-across-regions.md) | 
> | SKU | No |  

## <a name="microsoftexperimentation"></a>Microsoft. kísérletezés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | experimentworkspaces | No | 

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | névterek | No | 

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | featureproviders | No | 
> | funkciók | No | 
> | szolgáltatók | No | 
> | subscriptionfeatureregistrations | No | 

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | No | 

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | automanagedaccounts | No | 
> | automanagedvmconfigurationprofiles | No | 
> | guestconfigurationassignments | No | 
> | szoftver | No | 
> | softwareupdateprofile | No | 
> | softwareupdates | No | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | hanainstances | No | 
> | sapmonitors | No |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | dedicatedhsms | No | 


## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | No | 

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | gépek | No | 
> | gépek/bővítmények | No |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datamanagers |  No | 

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | eszközök | No | 
> | vnfs | No | 

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | összetevők | No | 
> | networkscopes | No | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | feladatok |  No | 

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem. [További információk](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region). |
> | actiongroups |  No | 
> | activitylogalerts | No | 
> | alertrules |  No | 
> | autoscalesettings |  No | 
> | alapkonfiguráció | No |
> | összetevők |  No |  
> | datacollectionrules | No | 
> | diagnosticsettings | No | 
> | diagnosticsettingscategories | No | 
> | eventcategories | No | 
> | eventtypes | No | 
> | extendeddiagnosticsettings | No |
> | guestdiagnosticsettings | No | 
> | listmigrationdate | No | 
> | logdefinitions | No | 
> | logprofiles | No | 
> | naplók | No |
> | metricalerts | No | 
> | metricbaselines | No | 
> | metricbatch | No | 
> | metricdefinitions | No | 
> | metricnamespaces | No | 
> | metrics | No | 
> | migratealertrules | No |
> | migratetonewpricingmodel | No | 
> | myworkbooks | No |
> | notificationgroups | No | 
> | privatelinkscopes | No |
> | rollbacktolegacypricingmodel | No |
> | scheduledqueryrules |  No | 
> | topology | No |
> | transactions | No |
> | vminsightsonboardingstatuses | No |
> | webteszteket |  No | 
> | webtesztek/gettestresultfile | No |
> | munkafüzetek |  No |  
> | workbooktemplates | No |


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | apptemplates | No | 
> | iotapps | No | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> |  iothub |  Igen (klónozási központ) <br/><br/> [IoT-központ klónozása egy másik régióba](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése |
> | ------------- | ----------- | 
> | Graph | No | 

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | deletedvaults | No |
> | hsmpools | No | 
> | managedhsms | No |
> | boltívek |  No | 

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | connectedclusters | No | 
> | registeredsubscriptions | No | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | No | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök |  No |  

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | labaccounts | No | 
> | felhasználók | No | 

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | No | 

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem, ez egy globális szolgáltatás.

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | hostingenvironments | No | 
> | integrationaccounts |  No |  
> | integrationserviceenvironments | No | 
> | integrationserviceenvironments/król | No |
> | isolatedenvironments | No | 
> | munkafolyamatok |  No |  

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | commitmentplans |  No | 
> | WebServices |  No | 
> | munkaterületek |  No | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | operationalizationclusters |  No | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | No | 
> | teamaccounts | No | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | No | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületek | No | 

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | configurationassignments | Igen. [További információ](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Igen. [További információ](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | frissítések | No | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | identitások | No | 
> | userassignedidentities | No | 

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | managednetworks | No | 
> | managednetworks / managednetworkgroups | No |
> | managednetworks / managednetworkpeeringpolicies | No | 
> | értesítés | No | 

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | No | 
> | registrationassignments | No |
> | registrationdefinitions | No | 

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | getentities | No | 
> | managementgroups | No | 
> | managementgroups/beállítások | No | 
> | resources | No | 
> | starttenantbackfill | No | 
> | tenantbackfillstatus | No | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok |  Nem, Azure Maps térinformatikai szolgáltatás. 
> | fiókok/privateatlases | No

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kínál | No | 
> | offertypes | No | 
> | privategalleryitems | No | 
> | privatestoreclient | No | 
> | privatestores | No | 
> | termékek | No | 
> | közzétevők | No | 
> | Regisztráció | No | 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | classicdevservices | No | 

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | megállapodások | No | 
> | offertypes | No | 

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Mediaservices |  No | 
> | Mediaservices/liveevents |  No | 
> | Mediaservices/streamingendpoints |  No | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | appclusters | No | 

## <a name="microsoftmigrate"></a>Microsoft. Migrálás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | assessmentprojects | No | 
> | migrateprojects | No | 
> | movecollections | No
> | projektek | No | 

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | No | 
> | objectunderstandingaccounts | No | 
> | remoterenderingaccounts | No | 
> | spatialanchorsaccounts | No | 

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | netappaccounts | No | 
> | netappaccounts / capacitypools | No | 
> | netappaccounts/capacitypools/kötetek | No | 
> | netappaccounts/capacitypools/kötetek/mounttargets | No | 
> | netappaccounts/capacitypools/kötetek/Pillanatképek | No | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | applicationgateways | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | 
> | applicationsecuritygroups |  No |  
> | azurefirewalls |  No |  
> | bastionhosts | No | 
> | bgpservicecommunities | No |
> | kapcsolatok |  No | 
> | ddoscustompolicies |  No | 
> | ddosprotectionplans | No | 
> | dnszones |  No | 
> | expressroutecircuits | No | 
> | expressroutegateways | No | 
> | expressrouteserviceproviders | No | 
> | firewallpolicies | No |
> | frontdoors | No | 
> | ipallocations | No |
> | ipgroups | No |
> | loadbalancers | Yes <br/><br/> Az [Azure](../../resource-mover/tutorial-move-region-virtual-machines.md) -beli erőforrás-mozgató használatával belső és külső terheléselosztó helyezhető át. |
> | localnetworkgateways |  No | 
> | natgateways |  No | 
> | networkexperimentprofiles | No |
> | networkintentpolicies |  No | 
> | networkinterfaces | Yes <br/><br/> A hálózati adapterek áthelyezéséhez használja az [Azure erőforrás-mozgatót](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkprofiles | No | 
> | networksecuritygroups | Yes <br/><br/> A hálózati biztonsági csoportok (NGSs) áthelyezéséhez használja az [Azure erőforrás-mozgatót](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkwatchers |  No |  
> | networkwatchers / connectionmonitors |  No | 
> | networkwatchers / flowlogs |  No | 
> | networkwatchers / pingmeshes |  No | 
> | p2svpngateways | No | 
> | privatednszones |  No |  
> | privatednszones / virtualnetworklinks | No |
> | privatednszonesinternal | No |
> | privateendpointredirectmaps | No |
> | privateendpoints | No | 
> | privatelinkservices | No | 
> | nyilvános IP | Yes<br/><br/> A nyilvános IP-címek áthelyezéséhez használja az [Azure erőforrás-mozgatót](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | publicipprefixes | No | 
> | routefilters | No | 
> | routetables |  No | 
> | securitypartnerproviders | No |
> | serviceendpointpolicies |  No | 
> | trafficmanagergeographichierarchies | No | 
> | trafficmanagerprofiles |  No | 
> | trafficmanagerusermetricskeys | No |
> | virtualhubs | No | 
> | virtualnetworkgateways |  No |  
> | virtualnetworks |  No | 
> | virtualnetworktaps | No | 
> | virtualwans | No | 
> | vpngateways (virtuális WAN) | No | 
> | vpnsites (virtuális WAN) | No | 
> | vpnsites (virtuális WAN) | No |


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | névterek |  No | 
> | névterek/notificationhubs |  No |  

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | osnamespaces | No | 

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | hypervsites | No | 
> | importsites | No | 
> | serversites | No | 
> | vmwaresites | No | 

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | No | 
> | deletedworkspaces | No | 
> | linktargets | No | 
> | storageinsightconfigs | No |
> | munkaterületek | No |



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations |  No | 
> | megoldások | No |
> | kilátással |  No | 

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | legacypeerings | No | 
> | peerasns | No | 
> | peeringlocations | No | 
> | társviszonyok | No | 
> | peeringservicecountries | No | 
> | peeringservicelocations | No | 
> | peeringserviceproviders | No | 
> | peeringservices | No | 

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | policyevents | No | 
> | policystates | No | 
> | policytrackedresources | No | 
> | szervizelések | No | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> |  -konzolok | No |
> | irányítópultok | No | 
> | usersettings | No | 


## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | workspacecollections |  No | 

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kapacitások |  No | 

## <a name="microsoftpurview"></a>Microsoft. hatáskörébe

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | No | 

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | availableaccounts | No | 
> | providerregistrations | No | 
> | kibocsátások | No | 

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | munkaterületek | No | 

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | replicationeligibilityresults | No |
> | boltívek | Nem.<br/><br/> Az Azure-régiók Azure Backup Recovery Services-tárolóinak áthelyezése nem támogatott.<br/><br/> Azure Site Recovery Recovery Services-tárolójában [letilthatja és újból létrehozhatja](../../site-recovery/move-vaults-across-regions.md) a tárolót a célhelyen. | 

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | openshiftclusters | No | 

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | névterek |  No | 

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | lekérdezések |  No |  
> | resourcechangedetails | No | 
> | resourcechanges | No | 
> | resources | No | 
> | resourceshistory | No | 
> | subscriptionsstatus | No | 

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | childresources | No | 
> | emergingissues | No | 
> | események | No | 
> | metaadatok | No | 
> | értesítések | No | 

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése |
> | ------------- | ----------- |
> | deploymentScripts |  Yes<br/><br/>[A Microsoft. Resources erőforrásainak áthelyezése új régióba](microsoft-resources-move-regions.md) |
> | templateSpecs |  Yes<br/><br/>[A Microsoft. Resources erőforrásainak áthelyezése új régióba](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások |  No | 
> | saasresources | No | 


## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | resourcehealthmetadata | No |
> | searchservices |  No | 


## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | No | 
> | advancedthreatprotectionsettings | No | 
> | riasztások | No | 
> | allowedconnections | No | 
> | applicationwhitelistings | No | 
> | assessmentmetadata | No | 
> | értékelések | No | 
> | autodismissalertsrules | No | 
> | automatizálások szabványának létrehozásában | No | 
> | autoprovisioningsettings | No |
> | complianceresults | No | 
> | felelésről | No | 
> | datacollectionagents | No | 
> | devicesecuritygroups | No | 
> | discoveredsecuritysolutions | No | 
> | externalsecuritysolutions | No | 
> | informationprotectionpolicies | No | 
> | iotsecuritysolutions | No | 
> | iotsecuritysolutions / analyticsmodels | No | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | No | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | No | 
> | jitnetworkaccesspolicies | No | 
> | policies | No | 
> | pricings | No | 
> | regulatorycompliancestandards | No | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | No | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | No | 
> | securitycontacts | No | 
> | securitysolutions | No | 
> | securitysolutionsreferencedata | No | 
> | securitystatuses | No | 
> | securitystatusessummaries | No | 
> | servervulnerabilityassessments | No | 
> | beállítások | No | 
> | alértékelések | No |
> | feladatok | No | 
> | topológiák | No | 
> | workspacesettings | No | 

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | összesítések | No | 
> | alertrules | No | 
> | alertruletemplates | No | 
> | automationrules | No |
> | esetekben | No | 
> | dataconnectors | No | 
> | szervezetek | No | 
> | entityqueries | No |
> | incidensek | No | 
> | officeconsents | No | 
> | beállítások | No | 
> | threatintelligence | No | 

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | consoleservices | No | 

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | átjárók | No | 
> | csomópontok | No | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | névterek |  No | 
> | premiummessagingregions | No | 
> | SKU | No | 

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások | No | 
> | fürtök |  No |  
> | containergroups | No | 
> | containergroupsets | No | 
> | edgeclusters | No | 
> | managedclusters | No |
> | hálózatok | No | 
> | secretstores | No | 
> | volumes | No | 

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások |  No | 
> | containergroups | No | 
> | átjárók |  No | 
> | hálózatok |  No | 
> | titkok |  No | 
> | volumes |  No |  

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | kibocsátások | No | 

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | signalr |  No |  

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | hybridusebenefits | No | 

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | appliancedefinitions | No | 
> | berendezések | No | 
> | jitrequests | No | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | instancepools | No | 
> | helyek | No |
> | managedinstances | Yes <br/><br/> [További](../../azure-sql/database/move-resources-across-regions.md) információ a felügyelt példányok régiók közötti áthelyezéséről. | 
> | managedinstances/adatbázisok | Yes | 
> | kiszolgálók | Yes | 
> | kiszolgálók/adatbázisok | Yes <br/><br/> [További](../../azure-sql/database/move-resources-across-regions.md) információ az adatbázisok régiók közötti áthelyezéséről.<br/><br/> [További](../../resource-mover/tutorial-move-region-sql.md) információ az Azure-beli erőforrás-mozgató Azure SQL Database-adatbázisok áthelyezéséhez való használatáról.  | 
> | kiszolgálók/elasticpools | Yes <br/><br/> [További](../../azure-sql/database/move-resources-across-regions.md) információ a rugalmas készletek régiók közötti áthelyezéséről.<br/><br/> [További](../../resource-mover/tutorial-move-region-sql.md) információ az Azure-beli erőforrás-mozgató Azure SQL rugalmas készletek áthelyezéséhez való használatáról.  | 
> | virtualclusters | Yes | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  No |  
> | sqlvirtualmachines |  No |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storageaccounts | Yes<br/><br/> [Azure Storage-fiók áthelyezése másik régióba](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | gyorsítótárak | No | 

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices |  No | 

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kezelők | No | 

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | No |
> | streamingjobs |  No |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | környezetben | No | 
> | esetben | No | 

## <a name="microsoftsubscription"></a>Microsoft. előfizetés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | előfizetések | No | 

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | services | No | 
> | supporttickets | No | 

## <a name="microsoftsynapse"></a>Microsoft. szinapszis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | munkaterületek | No | 
> | munkaterületek/bigdatapools | No | 
> | munkaterületek/sqlpools | No | 


## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | környezetben |  No | 
> | környezetek/eventsources |  No |  
> | környezetek/referencedatasets |  No | 

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tárolja | No | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | imagetemplates | No | 

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | account |  No | 
> | fiók/bővítmény |  No | 
> | fiók/projekt |  No | 

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | arczones | No | 
> | resourcepools | No | 
> | vCenter | No | 
> | virtualmachines | No | 
> | virtualmachinetemplates | No | 
> | virtualnetworks | No | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | No | 
> | dedicatedcloudservices | No | 
> | virtualmachines | No | 

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | eszközök | No | 
> | vnfs | No | 

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | fiókok | No | 
> | tervek | No | 
> | registeredsubscriptions | No |


## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | availablestacks | No | 
> | billingmeters | No | 
> | tanúsítványok | No | 
> | connectiongateways |  No |  
> | kapcsolatok |  No |  
> | customapis |  No | 
> | deletedsites | No | 
> | deploymentlocations | No | 
> | georegions | No | 
> | hostingenvironments | No | 
> | kubeenvironments | No | 
> | publishingusers | No |
> | javaslatok | No | 
> | resourcehealthmetadata | No | 
> | Runtimes | No | 
> | kiszolgálófarmok | No |  
> | kiszolgálófarmok/eventgridfilters | N
> | helyek |  No | 
> | helyek/premieraddons |  No |  
> | helyek/bővítőhelyek |  No |  
> | sourcecontrols | No |
> | staticsites | No | 

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | multipleactivationkeys | No |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | deviceservices | No | 

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | számítási feladatok | No | 

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | összetevők | No |
> | componentssummary | No | 
> | monitorinstances | No | 
> | monitorinstancessummary | No | 
> | figyeli | No | 
## <a name="third-party-services"></a>Harmadik féltől származó szolgáltatások

A harmadik féltől származó szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.

## <a name="next-steps"></a>Következő lépések

[További](../../resource-mover/overview.md) információ az erőforrás-mozgató szolgáltatásról.

