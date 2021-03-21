---
title: 800-os korláttal nem rendelkező erőforrások száma
description: Azokat az Azure-erőforrásokat sorolja fel, amelyek több mint 800 példányt tartalmazhatnak egy erőforráscsoporthoz.
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 6736118f0713a27a91618173e06423530e6f10cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563113"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Az erőforrások nem korlátozódnak 800 példányra az erőforráscsoport esetében

Alapértelmezés szerint az egyes erőforráscsoportok esetében akár 800 példányt is üzembe helyezhet. Bizonyos erőforrástípusok azonban mentesülnek az 800-es példány korlátján. Ez a cikk azokat az Azure-erőforrásokat sorolja fel, amelyek több mint 800 példányt tartalmazhatnak egy erőforráscsoporthoz. Az összes többi erőforrás-típus 800 példányra van korlátozva.

Egyes erőforrástípusok esetében kapcsolatba kell lépnie az ügyfélszolgálattal, hogy el lehessen távolítani az 800-es példány korlátját. Ezek az erőforrástípusok ebben a cikkben vannak feltüntetve.

## <a name="microsoftautomation"></a>Microsoft. Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

* edgeSubscriptions
* linkedSubscriptions
* regisztrációk
* regisztrációk/customerSubscriptions
* regisztrációk/termékek

## <a name="microsoftbotservice"></a>Microsoft. BotService

* botServices – alapértelmezés szerint 800 példányra korlátozódik. Ezt a korlátot a támogatási szolgálattal növelheti.

## <a name="microsoftcompute"></a>Microsoft.Compute

* lemezek
* katalógusok
* galériák/lemezképek
* galériák/lemezképek/verziók
* images
* pillanatképek
* virtualMachineScaleSets – alapértelmezés szerint 800 példányra korlátozódik. Ezt a korlátot a támogatási szolgálattal növelheti.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

* kibocsátásiegység-forgalmi jegyzékek/buildTasks
* kibocsátásiegység-forgalmi jegyzékek/buildTasks/listSourceRepositoryProperties
* kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések
* kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések/listBuildArguments
* kibocsátásiegység-forgalmi jegyzékek/eventGridFilters
* kibocsátásiegység-forgalmi jegyzékek/replikálások
* kibocsátásiegység-forgalmi jegyzékek/feladatok
* kibocsátásiegység-forgalmi jegyzékek/webhookok

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

* esetben

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

* kiszolgálók

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

* flexibleServers
* kiszolgálók

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

* flexibleServers
* serverGroups
* kiszolgálók
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

* menetrend

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft. EventHub

* fürtök
* névterek

## <a name="microsoftexperimentation"></a>Microsoft. kísérletezés

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* szoftver
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

* gépek – legfeljebb 5 000 példányt támogat
* bővítmények – a virtuálisgép-bővítmények korlátlan számú példányát támogatja

## <a name="microsoftinsights"></a>Microsoft. bepillantások

* metricalerts

## <a name="microsoftlogic"></a>Microsoft. Logic

* integrationAccounts
* munkafolyamatok

## <a name="microsoftmedia"></a>Microsoft. Media

* Mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft. NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/kötetek
* netAppAccounts/capacityPools/kötetek/mountTargets
* netAppAccounts/capacityPools/kötetek/Pillanatképek

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
* dnszones/mind
* dnszones/rekordhalmazok
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
* privateDnsZones/mind
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* Nyilvános IP – alapértelmezés szerint 800 példányra korlátozódik. Ezt a korlátot a támogatási szolgálattal növelheti.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

* workspaceCollections – alapértelmezés szerint 800 példányra korlátozódik. Ezt a korlátot a támogatási szolgálattal növelheti.

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

* kapacitás – alapértelmezés szerint 800 példányra korlátozódik. Ezt a korlátot a támogatási szolgálattal növelheti.

## <a name="microsoftrelay"></a>Microsoft. Relay

* névterek

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

* feladatgyűjtemények

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* névterek

## <a name="microsoftsingularity"></a>Microsoft. szingularitás

* fiókok
* fiókok/accountQuotaPolicies
* fiókok/groupPolicies
* fiókok/feladatok
* fiókok/storageContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftsql"></a>Microsoft.Sql

* kiszolgálók/adatbázisok

## <a name="microsoftweb"></a>Microsoft. Web

* apiManagementAccounts/API-k
* helyek

## <a name="next-steps"></a>Következő lépések

A kvóták és korlátok teljes listáját az Azure- [előfizetés és a szolgáltatás korlátai, kvótái és megkötései](azure-subscription-service-limits.md)című témakörben tekintheti meg.
