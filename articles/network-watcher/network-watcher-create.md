---
title: Azure Network Watcher-példány létrehozása | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure-Network Watcher egy Azure-régióban az Azure Portal vagy más technológiák használatával, valamint a Network Watcher törlésének módját.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c308824afdcae5f5c04a316c199bad71ad84a429
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94961991"
---
# <a name="create-an-azure-network-watcher-instance"></a>Azure Network Watcher-példány létrehozása

Network Watcher egy regionális szolgáltatás, amely lehetővé teszi az Azure-ban, a-ben és az-ban lévő hálózati forgatókönyvek szintjének figyelését és diagnosztizálását. A forgatókönyvek szintjének figyelése lehetővé teszi a hibák diagnosztizálását a végpontok közötti hálózati szintű nézetben. A Network Watcher elérhető hálózati diagnosztikai és vizualizációs eszközök segítségével megismerheti, diagnosztizálhatja és elemezheti a hálózatát az Azure-ban. A Network Watcher Network Watcher erőforrás létrehozásával engedélyezett. Ez az erőforrás lehetővé teszi Network Watcher képességek kihasználását.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>A Network Watcher automatikusan engedélyezve van
Amikor virtuális hálózatot hoz létre vagy módosít az előfizetésben, a Network Watcher automatikusan engedélyezve lesz a virtuális hálózat régiójában. A Network Watcher automatikus engedélyezése az Ön erőforrásait nem érinti, és nem jár költségekkel.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Network Watcher automatikus engedélyezés
Ha a Network Watcher automatikus engedélyezését szeretné letiltani, a következő parancsok futtatásával teheti meg:

> [!WARNING]
> A Network Watcher automatikus engedélyezésének kiválasztása állandó változás. Ha a leválasztást követően nem tud bejelentkezni, [forduljon a támogatási szolgálathoz](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Network Watcher létrehozása a portálon

Navigáljon az **összes szolgáltatás**  >  **hálózatkezelés**  >  **Network Watcher**. Kiválaszthatja az összes olyan előfizetést, amelyhez engedélyezni szeretné a Network Watcher. Ez a művelet minden elérhető régióban létrehoz egy Network Watcher.

![Network Watcher létrehozása](./media/network-watcher-create/figure1.png)

Amikor engedélyezi Network Watcher a portálon, a Network Watcher példány neve automatikusan *NetworkWatcher_region_name* , ahol a *Region_name* megfelel az Azure-régiónak, ahol a példány engedélyezve van. Például az USA nyugati középső régiójában engedélyezett Network Watcher neve *NetworkWatcher_westcentralus*.

A Network Watcher példány automatikusan létrejön egy *NetworkWatcherRG* nevű erőforráscsoporthoz. Az erőforráscsoport akkor jön létre, ha még nem létezik.

Ha testre szeretné szabni egy Network Watcher-példány nevét és a hozzá tartozó erőforráscsoportot, használhatja a PowerShellt, az Azure CLI-t, az alábbi szakaszokban leírt REST API vagy ARMClient metódusokat. Az egyes beállításokban az erőforráscsoport előtt léteznie kell egy Network Watcher létrehozásához.  

## <a name="create-a-network-watcher-with-powershell"></a>Network Watcher létrehozása a PowerShell-lel

Network Watcher példányának létrehozásához futtassa a következő példát:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Network Watcher létrehozása az Azure CLI-vel

Network Watcher példányának létrehozásához futtassa a következő példát:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Network Watcher létrehozása a REST API

A ARMclient a PowerShell használatával hívja meg a REST API. A ARMClient a chocolatey-on található [ARMClient](https://chocolatey.org/packages/ARMClient) on chocolatey

### <a name="log-in-with-armclient"></a>Bejelentkezés a ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>A Network Watcher létrehozása

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="create-a-network-watcher-using-azure-quickstart-template"></a>Network Watcher létrehozása az Azure Gyorsindítás sablonnal

Network Watcher-példány létrehozásához tekintse meg ezt a rövid útmutató [sablont](https://azure.microsoft.com/resources/templates/101-networkwatcher-create/)

## <a name="delete-a-network-watcher-in-the-portal"></a>Network Watcher törlése a portálon

Navigáljon az **összes szolgáltatás**  >  **hálózatkezelés**  >  **Network Watcher**.

Ha még nem tette meg, válassza az Áttekintés lapot. A legördülő listából válassza ki azt az előfizetést, amelynél le szeretné tiltani a Network watchert.
A nyílra kattintva bontsa ki a kiválasztott előfizetéshez tartozó régiók listáját. A helyi menü eléréséhez használja a jobb oldalon található 3 pontot.
A Letiltás megkezdéséhez kattintson a "Network Watcher letiltása" elemre. A rendszer kérni fogja, hogy erősítse meg ezt a lépést. A folytatáshoz kattintson az Igen lehetőségre.
A portálon ezt külön kell elvégeznie minden előfizetéshez tartozó régióban.


## <a name="delete-a-network-watcher-with-powershell"></a>Network Watcher törlése a PowerShell-lel

Network Watcher példányának törléséhez futtassa a következő példát:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Következő lépések

Most, hogy Network Watcher egy példánya van, ismerkedjen meg az elérhető funkciókkal:

* [Topológia](./view-network-topology.md)
* [Csomagrögzítő](network-watcher-packet-capture-overview.md)
* [IP-forgalomellenőrző](network-watcher-ip-flow-verify-overview.md)
* [Következő ugrás](network-watcher-next-hop-overview.md)
* [Biztonságicsoport-nézet](network-watcher-security-group-view-overview.md)
* [NSG folyamat naplózása](network-watcher-nsg-flow-logging-overview.md)
* [Virtual Network átjáró hibaelhárítása](network-watcher-troubleshoot-overview.md)