---
title: A BGP-állapot és a metrikák megtekintése
titleSuffix: Azure VPN Gateway
description: A hibaelhárítással kapcsolatos fontos BGP-információk megtekintése.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 02/22/2021
ms.author: alzam
ms.openlocfilehash: 097568dddd5c8568d4523cdeb05ab0c889c31670
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748046"
---
# <a name="view-bgp-metrics-and-status-using-powershell"></a>BGP-mérőszámok és-állapot megtekintése a PowerShell használatával

A **Get-AzVirtualNetworkGatewayBGPPeerStatus** használatával megtekintheti az összes BGP-társat és az állapotot

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

A **Get-AzVirtualNetworkGatewayLearnedRoute** használatával megtekintheti az átjáró által a BGP-n keresztül megszerzett összes útvonalat.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

A **Get-AzVirtualNetworkGatewayAdvertisedRoute** segítségével megtekintheti az összes olyan útvonalat, amelyet az ÁTJÁRÓ a BGP-n keresztül hirdet a társainak.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott erőforrásokra, használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport törléséhez. Ez a parancs törli az erőforráscsoportot és a benne található összes erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ResourceGroupName
```

## <a name="next-steps"></a>Következő lépések

További információ a Azure PowerShell modullal kapcsolatban: [Azure PowerShell dokumentáció](/powershell/azure/).
