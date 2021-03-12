---
title: A BGP-állapot és a metrikák megtekintése
titleSuffix: Azure VPN Gateway
description: A hibaelhárítással kapcsolatos fontos BGP-információk megtekintése.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103148846"
---
# <a name="view-bgp-metrics-and-status"></a>BGP-metrikák és-állapot megtekintése

A BGP-metrikák és-állapotok a Azure Portal használatával vagy a Azure PowerShell használatával tekinthetők meg.

## <a name="azure-portal"></a>Azure Portal

A Azure Portal megtekintheti a BGP-partnereket, a megismert útvonalakat és a meghirdetett útvonalakat. Le is töltheti az ezeket az adatfájlokat tartalmazó. csv fájlokat.

1. A [Azure Portal](https://portal.azure.com)navigáljon a virtuális hálózati átjáróhoz.
1. A **figyelés** területen válassza a **BGP-társak** lehetőséget a BGP-társak oldal megnyitásához.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="A Azure Portal metrikáinak képernyőképe.":::

### <a name="learned-routes"></a>Megismert útvonalak

1. A portálon akár 50 megismert útvonal is megtekinthető.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="Képernyőkép a megismert útvonalakról.":::

1. Le is töltheti a megismert útvonalakat tartalmazó fájlt. Ha több mint 50 megismert útvonala van, az összeset megtekintheti, ha letölti és megtekinti a. csv fájlt. A letöltéshez válassza a **megtanult útvonalak letöltése** lehetőséget.

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="Képernyőkép a megismert útvonalak letöltéséről.":::
1. Ezután tekintse meg a fájlt.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="Képernyőkép a letöltött megtanult útvonalakról.":::

### <a name="advertised-routes"></a>Meghirdetett útvonalak

1. A hirdetett útvonalak megtekintéséhez válassza a megtekinteni kívánt hálózat végén található **...** elemet, majd kattintson a **meghirdetett útvonalak megtekintése** elemre.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="A hirdetett útvonalak megtekintését bemutató képernyőkép." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. A **partnernek hirdetett útvonalakon** legfeljebb 50 meghirdetett útvonalat tekinthet meg.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="Képernyőkép a meghirdetett útvonalakról.":::
1. Letöltheti a meghirdetett útvonalakat tartalmazó fájlt is. Ha több mint 50 hirdetett útvonala van, az összeset megtekintheti, ha letölti és megtekinti a. csv fájlt. A letöltéshez válassza a **meghirdetett útvonalak letöltése** lehetőséget.

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="Képernyőkép a letöltött meghirdetett útvonalak kiválasztásáról.":::
1. Ezután tekintse meg a fájlt.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="Képernyőkép a letöltött meghirdetett útvonalakról.":::

### <a name="bgp-peers"></a>BGP-társak

1. A portálon akár 50 BGP-társat is megtekintheti.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="Képernyőfelvétel a BGP-partnerekről.":::
1. Letöltheti a BGP-társak fájlját is. Ha több mint 50 BGP-társa van, az összeset megtekintheti, ha letölti és megtekinti a. csv fájlt. A letöltéshez válassza a **BGP-társak letöltése** lehetőséget a portál lapon.

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="Képernyőkép a BGP-társak letöltéséről.":::
1. Ezután tekintse meg a fájlt.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="Képernyőkép a letöltött BGP-partnerekről.":::

## <a name="powershell"></a>PowerShell

A **Get-AzVirtualNetworkGatewayBGPPeerStatus** használatával megtekintheti az összes BGP-társat és az állapotot.

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

## <a name="next-steps"></a>Következő lépések

A BGP-vel kapcsolatos további információkért lásd: [a BGP konfigurálása VPN Gatewayhoz](bgp-howto.md).
