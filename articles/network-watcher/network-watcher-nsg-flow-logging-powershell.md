---
title: NSG-forgalom naplóinak kezelése – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Ez az oldal azt ismerteti, hogyan kezelhetők a hálózati biztonsági csoportok folyamatnaplói az Azure Network Watcher PowerShell használatával
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 29340852cabcc77b7488f734a4677697b4a9b972
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535234"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Hálózati biztonsági csoport forgalomnaplóinak konfigurálása a PowerShell-rel

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

A hálózati biztonsági csoport forgalmi naplói az Network Watcher, amely lehetővé teszi a hálózati biztonsági csoporton keresztüli bejövő és bejövő IP-forgalom információinak megtekintését. Ezek a forgalmi naplók JSON formátumban vannak megírva, és szabályonként mutatják a kimenő és bejövő folyamatokat, a folyamat hálózati adapterét, a folyamat öt rekordos információit (forrás/cél IP-cím, forrás/célport, protokoll), valamint azt, hogy a forgalom engedélyezve vagy tiltva lett-e.

Az AzPowerShell különböző verzióira vonatkozó összes NSG-forgalomnapló-parancs részletes specifikációját itt [talál](https://docs.microsoft.com/powershell/module/az.network/#network-watcher)

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

Ahhoz, hogy a folyamatnaplózás sikeresen működjön, regisztrálni kell a **Microsoft.Insights** szolgáltatót. Ha nem biztos benne, hogy a **Microsoft.Insights** szolgáltató regisztrálva van-e, futtassa a következő szkriptet.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Hálózati biztonsági csoport forgalomnaplóinak és Traffic Analytics

A folyamatnaplók engedélyezésére való parancs az alábbi példában látható:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

A megadott tárfiókhoz nem lehet olyan hálózati szabály konfigurálva, amely a hálózati hozzáférést Microsoft-szolgáltatások virtuális hálózatokra korlátozza. A tárfiók lehet ugyanabban vagy egy másik Azure-előfizetésben, mint az NSG, amely számára engedélyezi a folyamatnaplót. Ha különböző előfizetéseket használ, mindkettőt ugyanathoz a bérlőhöz kell Azure Active Directory társítva. Az egyes előfizetések fiókjának rendelkeznie kell a [szükséges engedélyekkel.](required-rbac-permissions.md)

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Az Traffic Analytics hálózati biztonsági csoport forgalomnaplóinak letiltása

Az alábbi példa használatával tiltsa le a Traffic Analytics és a Flow-naplók használatát:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Folyamatnapló letöltése

A folyamatnapló tárolási helye a létrehozáskor van meghatározva. A tárfiókba mentett folyamatnaplók eléréséhez megfelelő eszköz a Microsoft Azure Storage Explorer, amely innen tölthető le:  https://storageexplorer.com/

Ha meg van adva egy tárfiók, a folyamat naplófájlja egy tárfiókba lesz mentve a következő helyen:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

A napló szerkezetével kapcsolatos információkért látogasson el a [Hálózati biztonsági csoport folyamatnapló áttekintése témakörre.](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [vizualizálhatja az NSG-forgalom naplóit a PowerBI segítségével](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Megtudhatja, hogyan [vizualizálhatja az NSG-forgalom naplóit nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
