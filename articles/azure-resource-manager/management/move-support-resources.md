---
title: Áthelyezési művelet támogatása erőforrástípus szerint
description: Felsorolja az új erőforráscsoportba, előfizetésbe vagy régióba áthelyezható Azure-erőforrástípusokat.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: a56a9e6f04aa800e16bbab0190ce7b41d87da590
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740087"
---
# <a name="move-operation-support-for-resources"></a>Erőforrások áthelyezési műveleteinek támogatása

Ez a cikk felsorolja, hogy egy Azure-erőforrástípus támogatja-e az áthelyezési műveletet. Emellett információkat nyújt az erőforrások áthelyezésének speciális feltételeiről is.

> [!IMPORTANT]
> A legtöbb esetben a gyermekerőforrás nem mozgatható a szülőerőforrástól függetlenül. A gyermekerőforrások erőforrástípusa a `<resource-provider-namespace>/<parent-resource>/<child-resource>` következő: . A például `Microsoft.ServiceBus/namespaces/queues` a gyermekerőforrása. `Microsoft.ServiceBus/namespaces` A szülőerőforrás áthelyezésekor a gyermekerőforrás is automatikusan át lesz helyezték. Ha ebben a cikkben nem lát gyermekerőforrást, feltételezheti, hogy a szülőerőforrással együtt van áthelyezve. Ha a szülőerőforrás nem támogatja az áthelyezést, a gyermekerőforrást nem lehet áthelyezni.

Ugrás egy erőforrás-szolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.a fiók](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
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
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Fog](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Fog](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
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
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
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
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | tartományi szolgáltatások | Nem | Nem |  Nem |

## <a name="microsoftaadiam"></a>microsoft.a fiók

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings (diagnosztikai beállítások) | Nem | Nem | Nem |
> | diagnosticsettingscategories | Nem | Nem | Nem |
> | privatelinkforazuread | Igen | Igen | Nem |
> | Bérlők | Igen | Igen | Nem |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | támogatásiproviderek | Nem | Nem | Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | Nem | Nem | Nem |
> | addsservices | Nem | Nem | Nem |
> | Ügynökök | Nem | Nem | Nem |
> | anonymousapiusers (névtelen felhasználók) | Nem | Nem | Nem |
> | konfiguráció | Nem | Nem | Nem |
> | naplók | Nem | Nem | Nem |
> | jelentések | Nem | Nem | Nem |
> | servicehealthmetrics (szolgáltatás-egészségmetriák) | Nem | Nem | Nem |
> | services | Nem | Nem | Nem |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Konfigurációk | Nem | Nem | Nem |
> | generaterecommendations | Nem | Nem | Nem |
> | metaadatok | Nem | Nem | Nem |
> | Ajánlások | Nem | Nem | Nem |
> | mellőzései | Nem | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | actionrules (műveletszabály) | Igen | Igen | Nem |
> | riasztások | Nem | Nem | Nem |
> | alertslist | Nem | Nem | Nem |
> | riasztásokmetadata | Nem | Nem | Nem |
> | alertssummary (riasztások) | Nem | Nem | Nem |
> | alertssummarylist | Nem | Nem | Nem |
> | smartdetectoralertrules (intelligens detectoralertrules) | Igen | Igen | Nem |
> | smartgroups (intelligens csoportok) | Nem | Nem | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Szerverek | Igen | Igen | Nem |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> A API Management termékváltozatra beállított szolgáltatás nem mozgatható.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback (jelentéscsatorna-visszacsatolás) | Nem | Nem | Nem |
> | szolgáltatás | Igen | Yes | Igen (sablon használatával) <br/><br/> [Helyezze API Management régiók között.](../../api-management/api-management-howto-migrate.md) |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | konfigurációtárolók | Igen | Igen | Nem |
> | configurationstores / eventgridfilters | Nem | Nem | Nem |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Tavaszi | Igen | Igen | Nem |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Lásd [App Service áthelyezési útmutatót.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | Nem | Nem | Igen (sablon használatával)<br/><br/> [Alkalmazás App Service áthelyezése másik régióba](../../app-service/manage-move-across-regions.md) |
> | appidentities (appidentities) | Nem | Nem | Nem |
> | Átjárók | Nem | Nem | Nem |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | igazolásiproviders | Igen | Igen | Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | klasszikusadminisztrátorok | Nem | Nem | Nem |
> | dataaliases (adataliasok) | Nem | Nem | Nem |
> | denyassignments | Nem | Nem | Nem |
> | elevateaccess (emelt szintűaccess) | Nem | Nem | Nem |
> | findorphanroleassignments | Nem | Nem | Nem |
> | Zárak | Nem | Nem | Nem |
> | engedélyek | Nem | Nem | Nem |
> | policyassignments (szabályzat-hozzárendelések) | Nem | Nem | Nem |
> | policydefinitions (szabályzatdefiníciók) | Nem | Nem | Nem |
> | policysetdefinitions | Nem | Nem | Nem |
> | privatelinkassociations | Nem | Nem | Nem |
> | resourcemanagementprivatelinks | Nem | Nem | Nem |
> | roleassignments (szerepkör-hozzárendelések) | Nem | Nem | Nem |
> | roleassignmentsusagemetrics | Nem | Nem | Nem |
> | roledefinitions (szerepkördefiníciók) | Nem | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> A runbooknak ugyanabban az erőforráscsoportban kell lennie, mint az Automation-fióknak.
>
> További információ: [Azure Automation-fiók áthelyezése egy másik előfizetésbe.](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | automationaccounts | Igen | Yes | Igen (sablon használatával) <br/><br/> [Georeplikáció használata](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts /configurations | Igen | Igen | Nem |
> | automationaccounts/runbookok | Igen | Igen | Nem |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | privateclouds | Igen | Igen | Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories (b2cdirectories) | Igen | Igen | Nem |
> | b2ctenants | Nem | Nem | Nem |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | datacontrollers (vezérlők) | Nem | Nem | Nem |
> | hybriddatamanagers (hibridadat-managers) | Nem | Nem | Nem |
> | postgresinstances | Nem | Nem | Nem |
> | sqlinstances | Nem | Nem | Nem |
> | sqlmanagedinstances | Nem | Nem | Nem |
> | sqlserverinstances | Nem | Nem | Nem |
> | sqlserverregistrations (sqlserverregistrations) | Igen | Igen | Nem |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | Nem | Nem | Nem |
> | Regisztrációk | Igen | Igen | Nem |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Klaszterek | Nem | Nem | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts (kötegek száma) | Igen | Yes | A Batch-fiókok nem helyezhetők át közvetlenül egy régióból egy másikba, de sablon használatával exportálhat, módosíthat és üzembe helyezheti a sablont az új régióban. <br/><br/> Tudnivalók a [Batch-fiókok régiók közötti áthelyezésével kapcsolatban](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | billingaccounts (számlázási fiókok) | Nem | Nem | Nem |
> | billingperiods (számlázási időszak) | Nem | Nem | Nem |
> | billingpermissions | Nem | Nem | Nem |
> | számlázástulajdonság | Nem | Nem | Nem |
> | billingroleassignments | Nem | Nem | Nem |
> | billingroledefinitions | Nem | Nem | Nem |
> | Szervezeti egységek | Nem | Nem | Nem |
> | enrollmentaccounts | Nem | Nem | Nem |
> | Számlák | Nem | Nem | Nem |
> | Transzferek | Nem | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | Nem | Nem | Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | Nem | Nem | Nem |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers (blokklánctagok) | Nem | Nem | Nem <br/><br/> A blokklánchálózat nem lehet csomópontokkal különböző régiókban. |
> | cordamembers (cordamemberek) | Nem | Nem | Nem |
> | Watchers | Nem | Nem | Nem |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | tokenservices | Nem | Nem | Nem |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | blueprintassignments | Nem | Nem | Nem |
> | Tervrajz | Nem | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | Igen | Igen | Nem |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Ha a Azure Cache for Redis-példány virtuális hálózattal van konfigurálva, a példányt nem lehet másik előfizetésbe áthelyezni. Lásd: [Hálózati áthelyezés korlátozásai.](./move-limitations/networking-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | redis | Igen | Igen | Nem |
> | redisenterprise (redisenterprise) | Nem | Nem | Nem |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations (alkalmazott lefoglalások) | Nem | Nem | Nem |
> | calculateexchange (kiszámítás) | Nem | Nem | Nem |
> | ár kiszámítása | Nem | Nem | Nem |
> | calculatepurchaseprice (kulcsár kiszámítása) | Nem | Nem | Nem |
> | Katalógusok | Nem | Nem | Nem |
> | commercialreservationorders | Nem | Nem | Nem |
> | Exchange | Nem | Nem | Nem |
> | reservationorders (foglalásrendelések) | Nem | Nem | Nem |
> | Foglalás | Nem | Nem | Nem |
> | resources | Nem | Nem | Nem |
> | validatereservationorder | Nem | Nem | Nem |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nem | Nem | Nem |
> | cdnwebapplicationfirewallpolicies | Igen | Igen | Nem |
> | élcsomópont | Nem | Nem | Nem |
> | Profilok | Igen | Igen | Nem |
> | profilok/végpontok | Igen | Igen | Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Lásd [App Service áthelyezési útmutatót.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | certificateorders (tanúsítványrendelések) | Igen | Igen | Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Lásd: [Útmutató a klasszikus üzembe helyezés áthelyezéséhez.](./move-limitations/classic-model-move-limitations.md) A klasszikus üzembe helyezési erőforrások áthelyezheti az előfizetések között az adott forgatókönyvre jellemző művelettel.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | képességek | Nem | Nem | Nem |
> | tartománynevek | Igen | Nem | Nem |
> | kvóták | Nem | Nem | Nem |
> | resourcetypes (erőforrástípusok) | Nem | Nem | Nem |
> | validatesubscriptionmoveavailability | Nem | Nem | Nem |
> | virtualmachines | Igen | Igen | Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Lásd: [Útmutató a klasszikus üzembe helyezés áthelyezéséhez.](./move-limitations/classic-model-move-limitations.md) A klasszikus üzembe helyezési erőforrások áthelyezheti az előfizetések között az adott forgatókönyvre jellemző művelettel.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | Nem | Nem | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Lásd: [Útmutató a klasszikus üzembe helyezés áthelyezéséhez.](./move-limitations/classic-model-move-limitations.md) A klasszikus üzembe helyezési erőforrások áthelyezheti az előfizetések között az adott forgatókönyvre jellemző művelettel.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | képességek | Nem | Nem | Nem |
> | expressroutecsconnections | Nem | Nem | Nem |
> | expressroutecsconnections /társviszonyok | Nem | Nem | Nem |
> | gatewaysupporteddevices (átjárókupportáltdevices) | Nem | Nem | Nem |
> | networksecuritygroups | Nem | Nem | Nem |
> | kvóták | Nem | Nem | Nem |
> | reservedips | Nem | Nem | Nem |
> | virtualnetworks | Nem | Nem | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Lásd: [Útmutató a klasszikus üzembe helyezés áthelyezéséhez.](./move-limitations/classic-model-move-limitations.md) A klasszikus üzembe helyezési erőforrások áthelyezheti az előfizetések között az adott forgatókönyvre jellemző művelettel.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Lemezek | Nem | Nem | Nem |
> | images | Nem | Nem | Nem |
> | oszimages | Nem | Nem | Nem |
> | osplatformimages | Nem | Nem | Nem |
> | publicimages | Nem | Nem | Nem |
> | kvóták | Nem | Nem | Nem |
> | storageaccounts (tárfiókok) | Igen | Nem | Igen |
> | vmimages | Nem | Nem | Nem |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Lásd: [Útmutató a klasszikus üzembe helyezés áthelyezéséhez.](./move-limitations/classic-model-move-limitations.md) A klasszikus üzembe helyezési erőforrások áthelyezheti az előfizetések között az adott forgatókönyvre jellemző művelettel.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | műveletek | Nem | Nem | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Igen | Igen | Nem |
> | Cognitive Search | **Függőben lévő** | **Függőben lévő** | Manuális lépésekkel támogatott.<br/><br/> Tudnivalók a [szolgáltatás Azure Cognitive Search másik régióba való áthelyezésről](../../search/search-howto-move-across-regions.md) |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard (ratecard) | Nem | Nem | Nem |
> | usageaggregates | Nem | Nem | Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Lásd [az Virtual Machines útmutatót.](./move-limitations/virtual-machines-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | rendelkezésre állási készlet | Igen | Igen |  Yes <br/><br/> A [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) készleteket a rendelkezésre állási készletek áthelyezésére használhatja. |
> | diskaccesses | Nem | Nem | Nem |
> | diskencryptionsets | Nem | Nem | Nem |
> | Lemezek | Igen | Igen | Yes <br/><br/> Az [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) azure-beli virtuális gépek és a kapcsolódó lemezek áthelyezésére. |
> | Galériák | Nem | Nem | Nem |
> | katalógusok /képek | Nem | Nem | Nem |
> | katalógusok / képek / verziók | Nem | Nem | Nem |
> | gazdagépcsoportok | Nem | Nem | Nem |
> | gazdagépcsoportok /gazdagépek | Nem | Nem | Nem |
> | images | Igen | Igen | Nem |
> | proximityplacementgroups | Igen | Igen | Nem |
> | restorepointcollections | Nem | Nem | Nem |
> | restorepointcollections / restorepoints | Nem | Nem | Nem |
> | sharedvmextensions (megosztottvmextensions) | Nem | Nem | Nem |
> | sharedvmimages | Nem | Nem | Nem |
> | sharedvmimages / verziók | Nem | Nem | Nem |
> | pillanatképek | Igen | Igen | Nem |
> | sshpublickeys | Nem | Nem | Nem |
> | virtualmachines | Igen | Igen | Yes <br/><br/> Az [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) Azure-beli virtuális gépek áthelyezéshez használja a következőt: . |
> | virtualmachines / bővítmények | Igen | Igen | Nem |
> | virtualmachinescalesets | Igen | Igen | Nem |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregált költség | Nem | Nem | Nem |
> | Egyenlegek | Nem | Nem | Nem |
> | Költségvetések | Nem | Nem | Nem |
> | Díjak | Nem | Nem | Nem |
> | költségcímkék | Nem | Nem | Nem |
> | Hitelek | Nem | Nem | Nem |
> | események | Nem | Nem | Nem |
> | Előrejelzések | Nem | Nem | Nem |
> | Sok | Nem | Nem | Nem |
> | Piacokon | Nem | Nem | Nem |
> | árrések | Nem | Nem | Nem |
> | Termékek | Nem | Nem | Nem |
> | reservationdetails (foglalási részletek) | Nem | Nem | Nem |
> | reservationrecommendationdetails | Nem | Nem | Nem |
> | reservationrecommendations | Nem | Nem | Nem |
> | reservationsummaries (foglalások) | Nem | Nem | Nem |
> | foglalástranzakciók | Nem | Nem | Nem |
> | tags | Nem | Nem | Nem |
> | Bérlők | Nem | Nem | Nem |
> | Feltételek | Nem | Nem | Nem |
> | usagedetails (részletek) | Nem | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | containergroups (tárolócsoportok) | Nem | Nem | Nem |
> | serviceassociationlinks | Nem | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Nyilvántartások | Igen | Igen | Nem |
> | beállítás-nyilvántartások/ügynökkészletek | Igen | Igen | Nem |
> | beállításregisztrálók /buildtasks | Igen | Igen | Nem |
> | beállításregisztrációs tartományvezérlők/replikációk | Igen | Igen | Nem |
> | beállításregisztrálók /feladatok | Igen | Igen | Nem |
> | beállításregisztrálók /webhookok | Igen | Igen | Nem |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | Nem | Nem | Nem |
> | managedclusters (felügyelt fürtök) | Nem | Nem | Nem |
> | openshiftmanagedclusters | Nem | Nem | Nem |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | alkalmazások | Nem | Nem | Nem |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Nem | Nem | Nem |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | riasztások | Nem | Nem | Nem |
> | billingaccounts | Nem | Nem | Nem |
> | Költségvetések | Nem | Nem | Nem |
> | cloudconnectors (felhőcsatlakozók) | Nem | Nem | Nem |
> | összekötők | Igen | Igen | Nem |
> | Szervezeti egységek | Nem | Nem | Nem |
> | Méretek | Nem | Nem | Nem |
> | enrollmentaccounts | Nem | Nem | Nem |
> | Export | Nem | Nem | Nem |
> | externalbillingaccounts | Nem | Nem | Nem |
> | forecast | Nem | Nem | Nem |
> | lekérdezés | Nem | Nem | Nem |
> | Regisztráció | Nem | Nem | Nem |
> | reportconfigs (jelentéskonfigák) | Nem | Nem | Nem |
> | jelentések | Nem | Nem | Nem |
> | beállítások | Nem | Nem | Nem |
> | showbackrules (visszacsatolásiszabály) | Nem | Nem | Nem |
> | Kilátás nyílik | Nem | Nem | Nem |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Csomópontok | Nem | Nem | Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Kérelmek | Nem | Nem | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Egyesületek | Nem | Nem | Nem |
> | resourceproviders (erőforrás-betulajdonosok) | Igen | Igen | Nem |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Munkahelyek | Nem | Nem | Nem |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | Nem | Nem | Nem |
> | databoxedgedevices (databoxedgedevices) | Nem | Nem | Nem |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | munkaterületek | Nem | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Katalógusok | Igen | Igen | Nem |
> | datacatalogs | Nem | Nem | Nem |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionmanagers | Nem | Nem | Nem |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Csomagok | Nem | Nem | Nem |
> | Tervek | Nem | Nem | Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | datafactories | Igen | Igen | Nem |
> | Gyárak | Igen | Igen | Nem |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | Nem | Nem | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Igen | Igen | Nem |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Igen | Igen | Nem |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Nem | Nem | Nem |
> | szolgáltatások/projektek | Nem | Nem | Nem |
> | Bővítőhely | Nem | Nem | Nem |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults (biztonsági mentések) | Nem | Nem | Nem |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Igen | Igen | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Szerverek | Igen | Yes | A régiók közötti olvasási replikával áthelyezhet egy meglévő kiszolgálót. [További információ](../../postgresql/howto-move-regions-portal.md).<br/><br/> Ha a szolgáltatás georedundáns biztonsági mentési tárolóval van kiépítve, a georedundáns visszaállítással más régiókban is visszaállíthatja a szolgáltatást. [További információ](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers (rugalmas kiszolgáló) | Nem | Nem | Nem |
> | Szerverek | Igen | Yes | A régiók közötti olvasási replikával áthelyezhet egy meglévő kiszolgálót. [További információ](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Nem | Nem | Nem |
> | kiszolgálócsoportok | Nem | Nem | Nem |
> | Szerverek | Igen | Yes | A régiók közötti olvasási replikával áthelyezhet egy meglévő kiszolgálót. [További információ](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Igen | Igen | Nem |
> | singleservers (egykiszolgálós) | Igen | Igen | Nem |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | Igen | Igen | Nem |
> | bevezetések | Igen | Igen | Nem |
> | szolgáltatástopológiák | Igen | Igen | Nem |
> | szolgáltatástopológiák / szolgáltatások | Igen | Igen | Nem |
> | servicetopologies / services / serviceunits | Igen | Igen | Nem |
> | lépések | Igen | Igen | Nem |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | alkalmazáscsoportok | Igen | Igen | Nem |
> | gazdagépkészletek | Igen | Igen | Nem |
> | munkaterületek | Igen | Igen | Nem |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | elasticpools | Nem | Nem | Nem. Az erőforrás nincs elérhetővé téve. |
> | elasticpools / iothubtenants | Nem | Nem | Nem. Az erőforrás nincs elérhetővé téve. |
> | iothubs | Igen | Igen | Igen. [További információ](../../iot-hub/iot-hub-how-to-clone.md) |
> | kiépítési szolgáltatások | Igen | Igen | Nem |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Csővezetékek | Igen | Igen | Nem |
> | Vezérlők | **Függőben lévő** | **Függőben lévő** | No |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Vezérlők | Igen | Igen | Nem |
> | AKS-fürt | **Függőben lévő** | **Függőben lévő** | No<br/><br/> [További információ](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) a másik régióba való áthelyezésről.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | labcenters | Nem | Nem | Nem |
> | Labs | Igen | Nem | Nem |
> | tesztkörnyezetek/környezetek | Igen | Igen | Nem |
> | labs /servicerunners | Igen | Igen | Nem |
> | tesztkörnyezetek /virtualmachines | Igen | Nem | Nem |
> | Menetrend | Igen | Igen | Nem |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | Nem | Nem | Igen, az erőforrások új régióban való újralétrehozása által. [További információ](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames (adatbázis-fiókok nevei) | Nem | Nem | Nem |
> | databaseaccounts (adatbázis-fiókok) | Igen | Igen | Nem |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Tartományok | Igen | Igen | Nem |
> | generatessorequest | Nem | Nem | Nem |
> | topleveldomains (legfelső szintűtartományok) | Nem | Nem | Nem |
> | validatedomainregistrationinformation | Nem | Nem | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Igen | Igen | Nem |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Tartományok | Igen | Igen | Nem |
> | események előírói | Nem – nem áthelyezheti egymástól függetlenül, de automatikusan áthelyezheti az előfizetett erőforrással. | Nem – nem áthelyezheti egymástól függetlenül, de automatikusan áthelyezheti az előfizetett erőforrással. | No |
> | extensiontopics (bővítménytopika) | Nem | Nem | Nem |
> | partnernévterek | Igen | Igen | Nem |
> | partnerrégiók | Nem | Nem | Nem |
> | partnertopics | Igen | Igen | Nem |
> | rendszertopika | Igen | Igen | Nem |
> | Témák | Igen | Igen | Nem |
> | topictypes (témakörtípusok) | Nem | Nem | Nem |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Klaszterek | Igen | Igen | Nem |
> | Névterek | Igen | Yes | Igen (sablonnal)<br/><br/> [Eseményközpont-névtér áthelyezése másik régióba](../../event-hubs/move-across-regions.md) |
> | Sku | Nem | Nem | Nem |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | Nem | Nem | Nem |

## <a name="microsoftfalcon"></a>Microsoft.Fog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Névterek | Igen | Igen | Nem |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | featureproviders | Nem | Nem | Nem |
> | funkciók | Nem | Nem | Nem |
> | Szolgáltatók | Nem | Nem | Nem |
> | subscriptionfeatureregistrations | Nem | Nem | Nem |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Nem | Nem | Nem |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | automanagedaccounts | Nem | Nem | Nem |
> | automanagedvmconfigurationprofiles | Nem | Nem | Nem |
> | guestconfigurationassignments | Nem | Nem | Nem |
> | Szoftver | Nem | Nem | Nem |
> | softwareupdateprofile | Nem | Nem | Nem |
> | softwareupdates (szoftveres frissítés) | Nem | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | hanainstances | Nem | Nem | Nem |
> | sapmonitors (sapmonitors) | Nem | Nem | Nem |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedhsms | Nem | Nem | Nem |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> A HDInsight-fürtök áthelyezheti egy új előfizetésbe vagy erőforráscsoportba. Az előfizetések között azonban nem mozgathatja a HDInsight-fürthöz csatolt hálózati erőforrásokat (például a virtuális hálózatot, a hálózati adaptert vagy a terheléselosztást). Emellett nem térhet át új erőforráscsoportra olyan hálózati adapterre, amely a fürt virtuális gépéhez van csatolva.
>
> HdInsight-fürt új előfizetésbe való áthelyezésekor először helyezzen át más erőforrásokat (például a tárfiókot). Ezután helyezze át a HDInsight-fürtöt önmagában.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Klaszterek | Igen | Igen | Nem |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Igen | Igen | Nem |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Gépek | Igen | Igen | Nem |
> | gépek/bővítmények | Igen | Igen | Nem |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | datamanagers | Igen | Igen | Nem |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | eszközök | Nem | Nem | Nem |
> | vnfs | Nem | Nem | Nem |

## <a name="microsofthydra"></a>Microsoft.Fog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Összetevők | Nem | Nem | Nem |
> | networkscopes | Nem | Nem | Nem |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Munkahelyek | Igen | Igen | Nem |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> Győződjön meg arról, hogy az új előfizetésre való áthelyezés nem haladja meg az [előfizetési kvótákat.](azure-subscription-service-limits.md#azure-monitor-limits)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | **Függőben lévő** | **Függőben lévő** | Nem. [További információ](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region). |
> | actiongroups (műveletcsoportok) | Igen | Igen | Nem |
> | activitylogalerts (tevékenységlogalertek) | Nem | Nem | Nem |
> | alertrules (riasztási riasztások) | Igen | Igen | Nem |
> | automatikus méretezési beállítások | Igen | Igen | Nem |
> | alapkonfiguráció | Nem | Nem | Nem |
> | Összetevők | Igen | Igen | Nem |
> | datacollectionrules (adatoszlopok) | Nem | Nem | Nem |
> | diagnosticsettings (diagnosztikai beállítások) | Nem | Nem | Nem |
> | diagnosticsettingscategories | Nem | Nem | Nem |
> | eventcategories (eseménykategóriák) | Nem | Nem | Nem |
> | eventtypes (eseménytípusok) | Nem | Nem | Nem |
> | extendeddiagnosticsettings | Nem | Nem | Nem |
> | guestdiagnosticsettings | Nem | Nem | Nem |
> | listmigrationdate (listamigrációdate) | Nem | Nem | Nem |
> | logdefinitions (naplódefiníciók) | Nem | Nem | Nem |
> | logprofiles | Nem | Nem | Nem |
> | naplók | Nem | Nem | Nem |
> | metricalerts (mérőszám-mérőszámok) | Nem | Nem | Nem |
> | metricbaselines | Nem | Nem | Nem |
> | metricbatch (metrikabatch) | Nem | Nem | Nem |
> | metricdefinitions (metrikadefiníciók) | Nem | Nem | Nem |
> | metricnamespaces (metrikanévterek) | Nem | Nem | Nem |
> | metrics | Nem | Nem | Nem |
> | migratealertrules | Nem | Nem | Nem |
> | migratetonewpricingmodel | Nem | Nem | Nem |
> | myworkbooks | Nem | Nem | Nem |
> | értesítési csoportok | Nem | Nem | Nem |
> | privatelinkscopes | Nem | Nem | Nem |
> | rollbacktolegacypricingmodel | Nem | Nem | Nem |
> | scheduledqueryrules (ütemezett lekérdezések) | Igen | Igen | Nem |
> | topology | Nem | Nem | Nem |
> | transactions | Nem | Nem | Nem |
> | vminsightsonboardingstatuses | Nem | Nem | Nem |
> | webtests | Igen | Igen | Nem |
> | webtests / gettestresultfile | Nem | Nem | Nem |
> | munkafüzetek | Igen | Igen | Nem |
> | workbooktemplates | Igen | Igen | Nem |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates (apptemplates) | Nem | Nem | Nem |
> | iotapps | Igen | Igen | Nem |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | iothub | **Függőben lévő** | **Függőben lévő** | Igen (klónozási központ) <br/><br/> [IoT Hub klónozása másik régióba](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Grafikon | Igen | Igen | Nem |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> A lemeztitkosításhoz használt kulcstartók nem mozgathatók egy előfizetésben vagy előfizetésben lévő erőforráscsoportba.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | törölt fájlok | Nem | Nem | Nem |
> | hsmpools | Nem | Nem | Nem |
> | managedhsms | Nem | Nem | Nem |
> | Boltívek | Igen | Igen | Nem |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | csatlakoztatott fürtök | Igen | Igen | Nem |
> | registeredsubscriptions | Nem | Nem | Nem |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations (forrásvezérlőkonfigurációk) | Nem | Nem | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Klaszterek | Igen | Igen | Nem |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | labaccounts | Nem | Nem | Nem |
> | felhasználók | Nem | Nem | Nem |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Nem | Nem | Nem |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Nem | Nem | Nem, ez egy globális szolgáltatás. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | üzemeltetési környezetek | Nem | Nem | Nem |
> | integrationaccounts | Igen | Igen | Nem |
> | integrationserviceenvironments | Igen | Nem | Nem |
> | integrationserviceenvironments / managedapis | Igen | Nem | Nem |
> | elkülönített környezetek | Nem | Nem | Nem |
> | Munkafolyamatok | Igen | Igen | Nem |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | commitmentplans (kötelezettségvállalásisíkok) | Nem | Nem | Nem |
> | webszolgáltatások | Igen | Nem | Nem |
> | munkaterületek | Igen | Igen | Nem |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationclusters | Nem | Nem | Nem |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Nem | Nem | Nem |
> | teamaccounts | Nem | Nem | Nem |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Nem | Nem | Nem |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | munkaterületek | Nem | Nem | Nem |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationassignments | Nem | Nem | Igen. [További információ](../../virtual-machines/move-region-maintenance-configuration.md) |
> | maintenanceconfigurations (karbantartáskonfigurációk) | Igen | Igen | Igen. [További információ](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | Frissítések | Nem | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Identitások | Nem | Nem | Nem |
> | userassignedidentities | Nem | Nem | Nem |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | Nem | Nem | Nem |
> | managednetworks / managednetworkgroups | Nem | Nem | Nem |
> | managednetworks / managednetworkpeeringpolicies | Nem | Nem | Nem |
> | értesítés | Nem | Nem | Nem |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | Nem | Nem | Nem |
> | regisztrációk hozzárendelései | Nem | Nem | Nem |
> | registrationdefinitions (regisztrációdefiníciók) | Nem | Nem | Nem |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities (entitások le getentities) | Nem | Nem | Nem |
> | managementgroups | Nem | Nem | Nem |
> | felügyeleti csoportok / beállítások | Nem | Nem | Nem |
> | resources | Nem | Nem | Nem |
> | starttenantbackfill | Nem | Nem | Nem |
> | tenantbackfillstatus | Nem | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Igen | Yes | Nem, Azure Maps térinformatikai szolgáltatás. |
> | fiókok /privát fiókok | Igen | Igen | Nem |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Kínál | Nem | Nem | Nem |
> | offertypes (ajánlattípusok) | Nem | Nem | Nem |
> | privategalleryitems | Nem | Nem | Nem |
> | privatestoreclient | Nem | Nem | Nem |
> | privatestores | Nem | Nem | Nem |
> | Termékek | Nem | Nem | Nem |
> | Kiadók | Nem | Nem | Nem |
> | Regisztráció | Nem | Nem | Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | Nem | Nem | Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceRendelés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Megállapodások | Nem | Nem | Nem |
> | offertypes (ajánlattípusok) | Nem | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | Igen | Igen | Nem |
> | mediaservices /liveevents | Igen | Igen | Nem |
> | mediaservices /streamingendpoints | Igen | Igen | Nem |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | appclusters (alkalmazás-fürtök) | Nem | Nem | Nem |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects (értékelési projektek) | Nem | Nem | Nem |
> | migrateprojects | Nem | Nem | Nem |
> | movecollections | Nem | Nem | Nem |
> | Projektek | Nem | Nem | Nem |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | Nem | Nem | Nem |
> | objectunderstandingaccounts | Nem | Nem | Nem |
> | remoterenderingaccounts | Igen | Igen | Nem |
> | spatialanchorsaccounts | Igen | Igen | Nem |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | Nem | Nem | Nem |
> | netappaccounts / capacitypools | Nem | Nem | Nem |
> | netappaccounts / capacitypools /volumes | Nem | Nem | Nem |
> | netappaccounts / capacitypools / volumes / mounttargets | Nem | Nem | Nem |
> | netappaccounts / capacitypools / volumes / snapshots | Nem | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Lásd: [Hálózati áthelyezési útmutató.](./move-limitations/networking-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateways | Nem | Nem | Nem |
> | applicationgatewaywebapplicationfirewallpolicies | Nem | Nem | Nem |
> | applicationsecuritygroups (alkalmazásbiztonsági csoportok) | Igen | Igen | Nem |
> | azurefirewalls | Nem | Nem | Nem |
> | bastionhosts | Nem | Nem | Nem |
> | bgpservicecommunities (bgpservicecommunities) | Nem | Nem | Nem |
> | Kapcsolatok | Igen | Igen | Nem |
> | ddoscustompolicies | Igen | Igen | Nem |
> | ddosprotectionplans | Nem | Nem | Nem |
> | dnszones | Igen | Igen | Nem |
> | expressroutecircuits | Nem | Nem | Nem |
> | expressroutegateways | Nem | Nem | Nem |
> | expressrouteserviceproviders | Nem | Nem | Nem |
> | firewallpolicies (tűzfalszabályok) | Igen | Igen | Nem |
> | frontdoors (előtők) | Nem | Nem | Nem |
> | ipallocations (ipallocations) | Igen | Igen | Nem |
> | ipgroups | Igen | Igen | Nem |
> | terheléselosztási terhelések | Igen – Alapszintű termékváltozat<br> Igen – Standard termékváltozat | Igen – Alapszintű termékváltozat<br>Nem – Standard termékváltozat | Yes <br/><br/> Belső [és Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) terheléselosztások áthelyezésére használja a terheléselosztást. |
> | localnetworkgateways | Igen | Igen | Nem |
> | natgateways | Nem | Nem | Nem |
> | networkexperimentprofiles | Nem | Nem | Nem |
> | networkintentpolicies | Igen | Igen | Nem |
> | networkinterfaces (hálózati arcok) | Igen | Igen | Yes <br/><br/> A [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) a NIC-k áthelyezéshez. |
> | networkprofiles (hálózati profilok) | Nem | Nem | Nem |
> | networksecuritygroups | Igen | Igen | Yes <br/><br/> Hálózati [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) csoportok (NGS-k) áthelyezéshez használja a hálózati biztonsági csoportokat. |
> | networkwatchers | Nem | Nem | Nem |
> | networkwatchers /connectionmonitors | Igen | Nem | Nem |
> | networkwatchers /flowlogs | Igen | Nem | Nem |
> | networkwatchers / pingmeshes | Igen | Nem | Nem |
> | p2svpngateways | Nem | Nem | Nem |
> | privatednszones (magánhálózati zónák) | Igen | Igen | Nem |
> | privatednszones / virtualnetworklinks | Igen | Igen | Nem |
> | privatednszonesinternal | Nem | Nem | Nem |
> | privateendpointredirectmaps | Nem | Nem | Nem |
> | privateendpoints (privát pontok) | Nem | Nem | Nem |
> | privatelinkservices | Nem | Nem | Nem |
> | publicipaddresses | Igen – Alapszintű termékváltozat<br>Igen – Standard termékváltozat | Igen – Alapszintű termékváltozat<br>Nem – Standard termékváltozat | Yes<br/><br/> A [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) IP-címek áthelyezésére használja a következőt: . |
> | publicipprefixes | Igen | Igen | Nem |
> | routefilters (útvonalszűrők) | Nem | Nem | Nem |
> | útvonaltáblák | Igen | Igen | Nem |
> | securitypartnerproviders (biztonságipartner-partnerek) | Igen | Igen | Nem |
> | serviceendpointpolicies | Igen | Igen | Nem |
> | trafficmanagergeographichierarchies | Nem | Nem | Nem |
> | trafficmanagerprofiles | Igen | Igen | Nem |
> | trafficmanagerprofiles / heatmaps | Nem | Nem | Nem |
> | trafficmanagerusermetricskeys | Nem | Nem | Nem |
> | virtualhubs | Nem | Nem | Nem |
> | virtualnetworkgateways | Igen | Igen | Nem |
> | virtualnetworks | Igen | Igen | Nem |
> | virtualnetworktaps | Nem | Nem | Nem |
> | virtualrouterek | Igen | Igen | Nem |
> | virtualwans | Nem | Nem |
> | vpngateways (Virtual WAN) | Nem | Nem | Nem |
> | vpnserverconfigurations | Nem | Nem | Nem |
> | vpnsites (Virtual WAN) | Nem | Nem | Nem |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Névterek | Igen | Igen | Nem |
> | névterek / notificationhubs | Igen | Igen | Nem |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | Igen | Igen | Nem |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | hipervsites (hipervsites) | Nem | Nem | Nem |
> | importites (importáló) | Nem | Nem | Nem |
> | kiszolgálók | Nem | Nem | Nem |
> | vmwaresites | Nem | Nem | Nem |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Győződjön meg arról, hogy az új előfizetésre való áthelyezés nem haladja meg az [előfizetési kvótákat.](azure-subscription-service-limits.md#azure-monitor-limits)
>
> A csatolt Automation-fiókkal kapcsolatos munkaterületek nem mozgathatók. Az áthelyezési művelet megkezdése előtt mindenképpen leválasztsa az Automation-fiókokat.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Klaszterek | Nem | Nem | Nem |
> | töröltworkspaces | Nem | Nem | Nem |
> | linktargets | Nem | Nem | Nem |
> | storageinsightconfigs | Nem | Nem | Nem |
> | munkaterületek | Igen | Igen | Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | felügyeleti társítások | Nem | Nem | Nem |
> | managementconfigurations (felügyeleti konfigurációk) | Igen | Igen | Nem |
> | megoldások | Igen | Igen | Nem |
> | Kilátás nyílik | Igen | Igen | Nem |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | legacypeerings | Nem | Nem | Nem |
> | peerasns (társhálózatok) | Nem | Nem | Nem |
> | társviszony-létesítés áthelyezései | Nem | Nem | Nem |
> | társviszonyok | Nem | Nem | Nem |
> | társviszony-létesítés szolgáltatásszámai | Nem | Nem | Nem |
> | társviszony-létesítés szolgáltatáshelyei | Nem | Nem | Nem |
> | peeringserviceproviders | Nem | Nem | Nem |
> | társviszony-létesítési szolgáltatások | Nem | Nem | Nem |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | policyevents | Nem | Nem | Nem |
> | policystates | Nem | Nem | Nem |
> | policytrackedresources | Nem | Nem | Nem |
> | szervizelések | Nem | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> |  -konzolok | Nem | Nem | Nem |
> | irányítópultok | Igen | Igen | Nem |
> | usersettings | Nem | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | workspacecollections (munkaterület-collections) | Igen | Igen | Nem |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Kapacitások | Igen | Igen | Nem |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ---------- |
> | fiókok | Nem | Nem | Nem |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ---------- |
> | fiókok | **Függőben lévő** | **Függőben lévő** | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | availableaccounts (elérhető fiókok) | Nem | Nem | Nem |
> | szolgáltatói regisztrációk | Nem | Nem | Nem |
> | bevezetések | Nem | Nem | Nem |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | munkaterületek | Nem | Nem | Nem |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Lásd: [Helyreállítási szolgáltatások áthelyezési útmutatója.](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | replikációeligibilityresults | Nem | Nem | Nem |
> | Boltívek | Igen | Igen | Nem.<br/><br/> A Recovery Services-tárolók Azure Backup Azure-régiók között nem támogatottak.<br/><br/> A Helyreállítási tárak a Azure Site Recovery területen letilthatja, majd újra létrehozhatja [a](../../site-recovery/move-vaults-across-regions.md) tárolót a célterületen. |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | Nem | Nem | Nem |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Névterek | Igen | Igen | Nem |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Lekérdezések | Igen | Igen | Nem |
> | resourcechangedetails | Nem | Nem | Nem |
> | erőforrás-váltások | Nem | Nem | Nem |
> | resources | Nem | Nem | Nem |
> | resourceshistory (erőforrások) | Nem | Nem | Nem |
> | subscriptionsstatus | Nem | Nem | Nem |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | Nem | Nem | Nem |
> | emergingissues (újonnan megjelenő) | Nem | Nem | Nem |
> | események | Nem | Nem | Nem |
> | metaadatok | Nem | Nem | Nem |
> | Értesítések | Nem | Nem | Nem |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Telepítések | Nem | Nem | Nem |
> | deploymentscripts | Nem | Nem | Igen<br/><br/>[Microsoft.Resources-erőforrások áthelyezése új régióba](microsoft-resources-move-regions.md) |
> | deploymentscripts / naplók | Nem | Nem | Nem |
> | Linkek | Nem | Nem | Nem |
> | Szolgáltatók | Nem | Nem | Nem |
> | erőforráscsoportok | Nem | Nem | Nem |
> | resources | Nem | Nem | Nem |
> | Előfizetések | Nem | Nem | Nem |
> | tags | Nem | Nem | Nem |
> | templatespecs | Nem | Nem | Igen<br/><br/>[Microsoft.Resources-erőforrások áthelyezése új régióba](microsoft-resources-move-regions.md) |
> | templatespecs /versions | Nem | Nem | Nem |
> | Bérlők | Nem | Nem | Nem |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | alkalmazások | Igen | Nem | Nem |
> | saasresources | Nem | Nem | Nem |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> Egy műveletben nem lehet áthelyezni több keresési erőforrást különböző régiókban. Ehelyett helyezze át őket külön műveletekben.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | Nem | Nem | Nem |
> | keresési szolgáltatások | Igen | Igen | Nem |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | Nem | Nem | Nem |
> | advancedthreatprotectionsettings | Nem | Nem | Nem |
> | riasztások | Nem | Nem | Nem |
> | engedélyezett összekapcsolások | Nem | Nem | Nem |
> | applicationwhitelistings | Nem | Nem | Nem |
> | assessmentmetadata | Nem | Nem | Nem |
> | értékelések | Nem | Nem | Nem |
> | autodismissalertsrules | Nem | Nem | Nem |
> | automatizálások | Igen | Igen | Nem |
> | autoprovisioningsettings | Nem | Nem | Nem |
> | complianceresults (megfelelőségresults) | Nem | Nem | Nem |
> | megfelelőségek | Nem | Nem | Nem |
> | datacollectionagents | Nem | Nem | Nem |
> | devicesecuritygroups | Nem | Nem | Nem |
> | felderített biztonságimegoldások | Nem | Nem | Nem |
> | externalsecuritysolutions | Nem | Nem | Nem |
> | informationprotectionpolicies | Nem | Nem | Nem |
> | iotsecuritysolutions | Igen | Igen | Nem |
> | iotsecuritysolutions / analyticsmodels | Nem | Nem | Nem |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nem | Nem | Nem |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nem | Nem | Nem |
> | jitnetworkaccesspolicies | Nem | Nem | Nem |
> | policies | Nem | Nem | Nem |
> | díjszabás | Nem | Nem | Nem |
> | regulatorycompliancestandards | Nem | Nem | Nem |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Nem | Nem | Nem |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nem | Nem | Nem |
> | securitycontacts (biztonságikontacts) | Nem | Nem | Nem |
> | biztonsági megoldás | Nem | Nem | Nem |
> | securitysolutionsreferencedata | Nem | Nem | Nem |
> | securitystatuses (biztonságiállapotok) | Nem | Nem | Nem |
> | securitystatusessummaries | Nem | Nem | Nem |
> | servervulnerabilityassessments | Nem | Nem | Nem |
> | beállítások | Nem | Nem | Nem |
> | részértékelések | Nem | Nem | Nem |
> | feladatok | Nem | Nem | Nem |
> | Topológiák | Nem | Nem | Nem |
> | workspacesettings (munkaterület-beállítások) | Nem | Nem | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Összesítései | Nem | Nem | Nem |
> | riasztási szabály | Nem | Nem | Nem |
> | alertruletemplates | Nem | Nem | Nem |
> | automationrules | Nem | Nem | Nem |
> | könyvjelzők | Nem | Nem | Nem |
> | Esetekben | Nem | Nem | Nem |
> | adatösszekötők | Nem | Nem | Nem |
> | Entitások | Nem | Nem | Nem |
> | entityqueries (entitáslekérdezés) | Nem | Nem | Nem |
> | incidensek | Nem | Nem | Nem |
> | officeconsents (officeconsents) | Nem | Nem | Nem |
> | beállítások | Nem | Nem | Nem |
> | fenyegetésintelligencia | Nem | Nem | Nem |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | konzolszolgáltatások | Nem | Nem | Nem |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Átjárók | Nem | Nem | Nem |
> | Csomópontok | Nem | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Névterek | Igen | Igen | Nem |
> | premiummessagingregions | Nem | Nem | Nem |
> | Sku | Nem | Nem | Nem |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | alkalmazások | Nem | Nem | Nem |
> | Klaszterek | Igen | Igen | Nem |
> | containergroups (tárolócsoportok) | Nem | Nem | Nem |
> | containergroupsets (tárolócsoportok) | Nem | Nem | Nem |
> | edgeclusters | Nem | Nem | Nem |
> | managedclusters (felügyelt fürtök) | Nem | Nem | Nem |
> | Hálózatok | Nem | Nem | Nem |
> | titkos kulcstárolók | Nem | Nem | Nem |
> | volumes | Nem | Nem | Nem |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | alkalmazások | Igen | Igen | Nem |
> | containergroups (tárolócsoportok) | Nem | Nem | Nem |
> | Átjárók | Igen | Igen | Nem |
> | Hálózatok | Igen | Igen | Nem |
> | Titkok | Igen | Igen | Nem |
> | volumes | Igen | Igen | Nem |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | bevezetések | Nem | Nem | Nem |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | signalr | Igen | Igen | Nem |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | hybridusebenefits | Nem | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions (alkalmazásdefiníciók) | Nem | Nem | Nem |
> | alkalmazások | Nem | Nem | Nem |
> | jitrequests | Nem | Nem | Nem |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Egy adatbázisnak és kiszolgálónak ugyanabban az erőforráscsoportban kell lennie. Az SQL-kiszolgáló áthelyezésekor az összes adatbázisa is át lesz helyezték. Ez a viselkedés az adatbázisok Azure SQL Database és Azure Synapse Analytics vonatkozik.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | példánykészletek | Nem | Nem | Nem |
> | Helyek | Igen | Igen | Nem |
> | managedinstances | Nem | Nem | Igen <br/><br/> [További információ a](../../azure-sql/database/move-resources-across-regions.md) felügyelt példányok régiók közötti áthelyezésről. |
> | managedinstances /databases | Nem | Nem | Igen |
> | Szerverek | Igen | Igen |Yes |
> | kiszolgálók /adatbázisok | Igen | Igen | Yes <br/><br/> [További információ az](../../azure-sql/database/move-resources-across-regions.md) adatbázisok régiók közötti áthelyezéséhez.<br/><br/> [További információ a](../../resource-mover/tutorial-move-region-sql.md) Azure Resource Mover adatbázisok Azure SQL való áthelyezésről.  |
> | servers /databases / backuplongtermretentionpolicies | Igen | Igen | Nem |
> | servers /elasticpools | Igen | Igen | Yes <br/><br/> [További információ a](../../azure-sql/database/move-resources-across-regions.md) rugalmas készletek régiók közötti áthelyezésével kapcsolatban.<br/><br/> [További információ a](../../resource-mover/tutorial-move-region-sql.md) rugalmas Azure Resource Mover rugalmas készletek Azure SQL való áthelyezésével kapcsolatban.  |
> | servers /jobaccounts | Igen | Igen | Nem |
> | servers /jobagents | Igen | Igen | Nem |
> | virtualclusters (virtuális fürtök) | Igen | Igen | Yes |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | Igen | Igen | Nem |
> | sqlvirtualmachines | Igen | Igen | Nem |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | storageaccounts (tárfiókok) | Igen | Igen | Yes<br/><br/> [Azure Storage-fiók áthelyezése másik régióba](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Elrejt | Nem | Nem | Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Igen | Igen | Nem |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Nem | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Nem | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Vezetők | Nem | Nem | Nem |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Stream Analytics futó állapotban a feladatok nem mozgathatók.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Klaszterek | Nem | Nem | Nem |
> | streamingjobs | Igen | Igen | Nem |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Környezetben | Nem | Nem | Nem |
> | Példányok | Nem | Nem | Nem |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Előfizetések | Nem | Nem | Nem |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Nem | Nem | Nem |
> | támogatási kets | Nem | Nem | Nem |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | munkaterületek | Nem | Nem | Nem |
> | munkaterületek /bigdatapool-ek | Nem | Nem | Nem |
> | workspaces /sqlpools | Nem | Nem | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Környezetben | Igen | Igen | Nem |
> | környezetek /eseményforrások | Igen | Igen | Nem |
> | környezetek /referencedatasets | Igen | Igen | Nem |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Üzletek | Igen | Igen | Nem |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | imagetemplates | Nem | Nem | Nem |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!IMPORTANT]
> Az Azure DevOps-előfizetés módosítása: [A számlázáshoz használt Azure-előfizetés módosítása.](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Nem | Nem | Nem |
> | fiók/bővítmény | Nem | Nem | Nem |
> | fiók/projekt | Nem | Nem | Nem |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones (arczónák) | Nem | Nem | Nem |
> | erőforráskészletek | Nem | Nem | Nem |
> | vcenterek | Nem | Nem | Nem |
> | virtualmachines | Nem | Nem | Nem |
> | virtualmachinetemplates | Nem | Nem | Nem |
> | virtualnetworks | Nem | Nem | Nem |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedcloudnodes (dedikált felhőcsomópont) | Nem | Nem | Nem |
> | dedicatedcloudservices | Nem | Nem | Nem |
> | virtualmachines | Nem | Nem | Nem |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | eszközök | Nem | Nem | Nem |
> | vnfs | Nem | Nem | Nem |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | fiókok | Nem | Nem | Nem |
> | Tervek | Nem | Nem | Nem |
> | registeredsubscriptions | Nem | Nem | Nem |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Lásd [App Service útmutatót.](./move-limitations/app-service-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | availablestacks | Nem | Nem | Nem |
> | billingmeters (számlázási mérők) | Nem | Nem | Nem |
> | tanúsítványok | Nem | Igen | Nem |
> | connectiongateways | Igen | Igen | Nem |
> | Kapcsolatok | Igen | Igen | Nem |
> | customapis (egyéniapis) | Igen | Igen | Nem |
> | törölt webhelyek | Nem | Nem | Nem |
> | üzembehelyezések | Nem | Nem | Nem |
> | georégiók | Nem | Nem | Nem |
> | üzemeltetéskörnyezetek | Nem | Nem | Nem |
> | kubeenvironments | Igen | Igen | Nem |
> | közzétételihasználók | Nem | Nem | Nem |
> | Ajánlások | Nem | Nem | Nem |
> | resourcehealthmetadata | Nem | Nem | Nem |
> | runtimes (futtatás) | Nem | Nem | Nem |
> | kiszolgálófarmok | Igen | Igen | Nem |
> | serverfarms / eventgridfilters | Nem | Nem | Nem |
> | Helyek | Igen | Igen | Nem |
> | sites /premieraddons | Igen | Igen | Nem |
> | helyek/tárolóhelyek | Igen | Igen | Nem |
> | sourcecontrols (forrásvezérlők) | Nem | Nem | Nem |
> | staticsites (statikus) | Nem | Nem | Nem |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | Nem | Nem | Nem |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | eszközszolgáltatások | Nem | Nem | Nem |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Munkaterhek | Nem | Nem | Nem |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés | Régió áthelyezése |
> | ------------- | ----------- | ---------- | ----------- |
> | Összetevők | Nem | Nem | Nem |
> | componentssummary (összetevők) | Nem | Nem | Nem |
> | figyeléstelepítések | Nem | Nem | Nem |
> | monitorinstancessummary | Nem | Nem | Nem |
> | Monitorok | Nem | Nem | Nem |

## <a name="third-party-services"></a>Külső szolgáltatások

A külső szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.

## <a name="next-steps"></a>Következő lépések

- Az erőforrások áthelyezési parancsaiért lásd: Erőforrások áthelyezése új [erőforráscsoportba vagy előfizetésbe.](move-resource-group-and-subscription.md)
- [További információ a](../../resource-mover/overview.md) Resource Mover szolgáltatásról.
- Ha vesszővel elválasztott értékeket is tartalmaz, töltse le az [move-support-resources.csv. ](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)
