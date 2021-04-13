---
title: 800 darabszámkorlát nélküli erőforrások
description: Felsorolja azokat az Azure-erőforrástípusokat, amelyek egy erőforráscsoportban több mint 800 példánysal is lehet.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: d132773ff35d53dc373c759326efc8179f4993d6
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366538"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Erőforráscsoportonként legfeljebb 800 példány

Alapértelmezés szerint minden erőforráscsoportban legfeljebb 800 erőforrástípust helyezhet üzembe. Egyes erőforrástípusok azonban mentesülnek a 800-as példánykorlát alól. Ez a cikk azokat az Azure-erőforrástípusokat sorolja fel, amelyek több mint 800 példánya lehet egy erőforráscsoportban. Minden más erőforrástípus legfeljebb 800 példány lehet.

Egyes erőforrástípusok esetében kapcsolatba kell lépnie az ügyfélszolgálattal, hogy eltávolítsa a 800-as példánykorlátot. Ezeket az erőforrástípusokat ebben a cikkben olvashatja.


## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

* resourceHealthAlertRules
* smartDetectorAlertRules

## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* edgeSubscriptions (edgeSubscriptions)
* linkedSubscriptions (linkedSubscriptions)
* Regisztrációk
* regisztrációk/customerSubscriptions
* regisztrációk/termékek

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices – Alapértelmezés szerint legfeljebb 800 példány lehet. Ez a korlát az ügyfélszolgálattal való kapcsolatfelvételkor növelhető.

## <a name="microsoftcompute"></a>Microsoft.Compute

* Lemezek
* Galériák
* katalógusok/képek
* katalógusok/képek/verziók
* images
* pillanatképek
* virtualMachineScaleSets – Alapértelmezés szerint legfeljebb 800 példány lehet. Ez a korlát az ügyfélszolgálattal való kapcsolatfelvételkor növelhető.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* beállításregisztrálók/buildTasks
* registries/buildTasks/listSourceRepositoryProperties
* beállításregisztrálók/buildTasks/steps
* registries/buildTasks/steps/listBuildArguments
* beállításregisztrálók/eventGridFilters
* beállításregisztrációs jegyzékek/replikációk
* beállításregisztrálók/feladatok
* beállításregisztrálók/webhookok

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

* Példányok

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* Szerverek

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers (rugalmas kiszolgáló)
* Szerverek

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers (rugalmas kiszolgáló)
* serverGroups (kiszolgálócsoportok)
* serverGroupsv2
* Szerverek
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* Menetrend

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* Klaszterek
* Névterek

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* Szoftver
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

* machines – legfeljebb 5000 példányt támogat
* gépek/bővítmények – korlátlan számú virtuálisgép-bővítménypéldányt támogat

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts (metrika-mérőszámok)

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts (integrationAccounts)
* Munkafolyamatok

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts (netAppAccounts)
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots
* netAppAccounts/volumeGroups

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses – Alapértelmezés szerint legfeljebb 800 példány lehet. Ez a korlát az ügyfélszolgálattal való kapcsolatfelvételkor növelhető.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections – Alapértelmezés szerint legfeljebb 800 példány lehet. Ez a korlát az ügyfélszolgálattal való kapcsolatfelvételen növelhető.

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* autoScaleVCores – Alapértelmezés szerint legfeljebb 800 példány lehet. Ez a korlát az ügyfélszolgálattal való kapcsolatfelvételen növelhető.
* capacities – Alapértelmezés szerint legfeljebb 800 példány lehet. Ez a korlát az ügyfélszolgálattal való kapcsolatfelvételen növelhető.

## <a name="microsoftrelay"></a>Microsoft.Relay

* Névterek

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections (feladatoszlopok)

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* Névterek

## <a name="microsoftsingularity"></a>Microsoft.Singularity

* fiókok
* accounts/accountQuotaPolicies
* accounts/groupPolicies
* fiókok/feladatok
* fiókok/storageContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts (tárfiókok)

## <a name="microsoftsql"></a>Microsoft.Sql

* kiszolgálók/adatbázisok

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* Helyek

## <a name="next-steps"></a>Következő lépések

A kvóták és korlátok teljes listájáért lásd: Azure-előfizetések és -szolgáltatások [korlátai, kvótái és megkötései.](azure-subscription-service-limits.md)
