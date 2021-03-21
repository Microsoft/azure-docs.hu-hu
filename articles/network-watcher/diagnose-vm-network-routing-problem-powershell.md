---
title: Virtuálisgép-hálózat útválasztási problémáinak diagnosztizálása – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Ebből a cikkből megtudhatja, hogyan diagnosztizálhatja a virtuális gépek hálózati útválasztási problémáit az Azure Network Watcher következő ugrási funkciója segítségével.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 6569a99ec851da478151665921e7689e1c3488f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020206"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Virtuálisgép-hálózat útválasztási problémáinak diagnosztizálása – Azure PowerShell

Ebben a cikkben üzembe helyez egy virtuális gépet (VM), majd megtekintheti a kommunikációt egy IP-címmel és egy URL-címmel. Meghatározza a kommunikációs hiba okát és feloldásának módját.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez a Azure PowerShell `Az` modulra van szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.



## <a name="create-a-vm"></a>Virtuális gép létrehozása

Mielőtt virtuális gépet hozhatna létre, létre kell hoznia egy erőforráscsoportot, amely majd tartalmazza a virtuális gépet. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Hozza létre a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm). Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a további lépések megtételét, amíg létre nem jön a virtuális gép, és a PowerShell vissza nem adja a kimenetet.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

A Network Watcherával folytatott hálózati kommunikáció teszteléséhez először engedélyeznie kell egy hálózati figyelőt abban a régióban, ahol a tesztelni kívánt virtuális gép be van kapcsolva, majd a kommunikáció teszteléséhez használja a Network Watcher következő ugrási képességét.

## <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha az USA keleti régiójában már engedélyezve van egy hálózati figyelő, a [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) használatával kérheti le a Network watchert. A következő példa egy meglévő, *NetworkWatcher_eastus* nevű hálózati figyelőt kér le, amely a *NetworkWatcherRG* erőforráscsoportban található:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Ha még nincs engedélyezve hálózati figyelő az USA keleti régiójában, a [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) használatával hozzon létre egy Network watchert az USA keleti régiójában:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>A következő ugrás használata

Az Azure automatikusan létrehoz útvonalakat az alapértelmezett célokhoz. Egyéni útvonalakat is létrehozhat, amelyekkel felülírhatja az Azure alapértelmezett útvonalait. Bizonyos esetekben az egyéni útvonalak kommunikációs hibákat eredményezhetnek. A virtuális gép útválasztásának teszteléséhez használja a [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) parancsot a következő útválasztási ugrás meghatározásához, ha a forgalom egy adott címnek van szánva.

Tesztelje a virtuális gép kimenő kommunikációját a www.bing.com IP-címeinek egyikén:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Néhány másodperc elteltével a kimenet tájékoztatja Önt arról, hogy a **NextHopType** **internetes**, és hogy a **RouteTableId** a **rendszer útvonala**. Ebből az eredményből megtudhatja, hogy van-e érvényes útvonal a célhelyre.

Tesztelje a virtuális gép kimenő kommunikációját a 172.31.0.100 címen:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

A visszaadott kimenet tájékoztatja, hogy **egyik sem** a **NextHopType**, és hogy a **RouteTableId** is a **rendszer útvonala**. Ez az eredmény azt jelzi, hogy létezik érvényes rendszerútvonal a cél felé, de nincs következő ugrás, hogy a forgalmat a cél felé irányítsa.

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

Az Útválasztás további elemzéséhez tekintse át a hálózati adapter érvényes útvonalait a [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) paranccsal:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

A rendszer a következő szöveget tartalmazó kimenetet adja vissza:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Ahogy az előző kimenetben is látható, a **0.0.0.0/0** **AddressPrefix** útvonalon az összes forgalom nem a másik útvonalon található címekre irányuló, az **Internet** következő ugrásával ellátott összes forgalmat átirányítja. Ahogy a kimenetben is látható, bár a 172.16.0.0/12 előtag alapértelmezett útvonala, amely magában foglalja a 172.31.0.100-címeket, a **nextHopType** **nincs.** Az Azure létrehoz egy alapértelmezett útvonalat a 172.16.0.0/12 címhez, de amíg nincs oka rá, nem határozza meg a következő ugrás típusát. Ha például hozzáadta a 172.16.0.0/12 címtartományt a virtuális hálózat címterület számára, az Azure a **NextHopType** **virtuális hálózatra** módosítja az útvonalon. Az ellenőrzések után a **virtuális hálózat** **nextHopType** jelenik meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozta a virtuális gépet, és diagnosztizálta a hálózati útválasztást a virtuális gépről. Megtudta, hogy az Azure számos alapértelmezett utat létrehoz, és tesztelte az útválasztást két különböző cél felé. További tudnivalók az [Azure-beli útválasztásról](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és az [egyéni útvonalak létrehozásáról](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

A kimenő virtuálisgép-kapcsolatok esetében meghatározhatja a virtuális gép és a végpont közötti hálózati forgalmat a Network Watcher [kapcsolódási hibákkal kapcsolatos](network-watcher-connectivity-powershell.md) funkciójának használatával is. Egy virtuális gép és egy végpont (például IP-cím vagy URL) közötti kommunikációt figyelheti az Network Watcher a kapcsolat figyelője funkciójának használatával. További információ: [hálózati kapcsolatok figyelése](connection-monitor.md).
